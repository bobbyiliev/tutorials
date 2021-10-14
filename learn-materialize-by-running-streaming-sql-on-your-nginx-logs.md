---

title: Learn Materialize by running streaming SQL on your Nginx logs
tags: sql,data,database,devops,materialize
image: https://cdn.devdojo.com/posts/images/October2021/learn-materialize-by-running-streaming-sql-on-your-nginx-logs2.jpg
status: draft

---

# Introduction 

[Materialize](https://materialize.com/) is a streaming database for real-time analytics and applications. 

In this tutorial, I will show you how to get started with Materialize. running streaming SQL on your Nginx logs. By the end of the tutorial, you will have some general knowledge of what Materialize is and how to get started.

# Prerequisites

For the sake of simplicity, I will use a brand new Ubuntu 21.04 server where I will install Materialize and `psql`.

If you want to follow along you could spin up a new Ubuntu 21.04 server on your favorite could provider. 

I'll be using DigitalOcean. If you are new to DigitalOcean, you could use my referral link to get a $100 Free Credit:

[DigitalOcean $100 Free Credit](https://m.do.co/c/2a9bba940f39)

# What is Materialize

Materialize is a streaming database for real-time analytics. 

Materialize is not a substitution for your transaction database. It accepts input data from a variety of sources like:

 * Streaming sources like Kafka
 * Data stores like S3
 * Databases like PostgreSQL
 * Files CSV, JSON and even unstructured files like logs

And it lets you query those sources using standard SQL.

![Materialize Landing Page](https://imgur.com/MU5IHOV.png)

If you want to learn more about Materialize, make sure to check out their official documentation here:

[Materialize Documentation](https://materialize.com/docs/)

# Installing Docker

As a first step, let's quickly install Docker on our Ubuntu server.

To do so, first SSH to the server. And then run the handy Docker installation script:

```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

With that, your Docker instance would be up and running and we are ready to start our Materialize container!

# Starting Materialize Container

There are other ways that you could use in order to run Materialize as described [here](https://materialize.com/docs/install/). But for this demo, we are just going to use Docker as it is quite convenient. For a production-ready Materialize instance, I would recommend giving [Materialize Cloud](https://materialize.com/product) a try!

To start a Materialize instance, just run the following Docker command:

```
docker run -p 6875:6875 materialize/materialized:v0.9.7 --workers 1
```

Now that we have the `materialized` container running, we would also need to install a CLI tool that we could use to interact with our Materialize instance!

# Installing `mzcli` 

The `mzcli` tool lets us connect to Materialize just as you would use `psql` to connect to PostgreSQL.

Speaking of `psql`, Materialize is fully compatible with `psql` so if you have `psql` already installed you could use it instead of `mzcli`.

> Note: if you already have `psql` installed you can skip this step.

To learn the main differences between the two, make sure to check out the official documentation here: [Materialize CLI Connections](https://materialize.com/docs/connect/cli/)

In order to install `mzcli`, you could use `pip` or `pipx`:

```
pipx install mzcli
```

> If you don't have `pipx` installed, you could install it with the `apt install pipx` command.

Or alternatively, you could run it as a Docker container too:

```
docker run -it materialize/mzcli mzcli --help
```

For this demo, I would stick to `pipx`.

To access, materialize you can use the following command:

```
mzcli -U materialize -h localhost -p 6875 materialize
```

The syntax would be the same for `psql` but with `mzcli` you would get some nice highlighting when writing your queries.

![](https://imgur.com/SyLQvlT.png)

# Creating a Materialize Source

By creating a Source you are essentially telling Materialize to connect to some data source. As described in the introduction, you could add a wide variety of sources to Materialize. 

For the full list of source types make sure to check out the official documentation here:

[Materialize source types](https://materialize.com/docs/sql/create-source/)

For this demo, let's quickly install Nginx and use Regex to parse the log and create Materialized Views.

## Install Nginx

If you already have an existing Nginx instance, you can skip this step.

In order to install Nginx all that you just need to run the following command:

```
sudo apt install nginx
```

Next, let's populate the access log with some entries with this simple Bash loop:

```
 for i in {1..200} ; do curl -s 'localhost/materialize'  > /dev/null ; echo $i ; done
```

Note that if you have an actual Nginx `access.log`, you can skip the step above.

Now we would have some entries in the `/var/log/nginx/access.log` access log file that we would be able to able to load into Materialize.

## Adding a Materialize source

Let's start by creating a Materialize source from our Nginx Access log.

First access the Materialize instance with the `mzcli` command:

```
mzcli -U materialize -h localhost -p 6875 materialize
```

Then run the following query:

```
CREATE SOURCE nginx_log 
FROM FILE '/var/log/nginx/access.log'  
WITH (tail = true)  
FORMAT REGEX '(?P<ipaddress>\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}).-.-.(?P<time>(\[.*\]))."(?P<request>((GET|POST|HEAD|PUT))).(?P<url>.+)(HTTP/.\..") (?P<statuscode>\d{3})';
```

A quick rundown of the query:

 * `CREATE SOURCE`: First we specify the name of our source
 * `FROM FILE`: Then we specify that we want to create a source from a local file and we provide the path to that file
 * `WITH (tail = true)`: Continually check the file for new content
 * `FORMAT REGEX`: as this is an unstructured file we need to specify regex as the format so that we could get only the specific parts of the log that we need. 

Let's quickly review the Regex itself as well.

> The important part to note down is the `?P<NAME_HERE>`, this essentially creates our SQL columns followed by our patter.

To make this a bit more clear, a standard entry in your Nginx access log file would look like this:

```
123.123.123.119 - - [13/Oct/2021:10:54:22 +0000] "GET / HTTP/1.1" 200 396 "-" "Mozilla/5.0 zgrab/0.x"
```

* `(?P<ipaddress>\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3})`: With this patter we match the IP address for each line of the Nginx log:

![Nginx log regex explained](https://imgur.com/GDHi5Lf.png)

* `(?P<time>(\[.*\]))`: Then we also get the timestamp which is right after the IP and is inside brackets:

![](https://imgur.com/bdjP5WH.png)

* `(?P<request>((GET|POST|HEAD|PUT)))`: Here we get the type of request like `GET`, `POST` and etc.:

![](https://imgur.com/cGGWmBJ.png)

* `(?P<url>.+)(HTTP/.\..")`: Here we define the URL matching pattern:

![](https://imgur.com/cGGWmBJ.png)

* `(?P<statuscode>\d{3})`: And finally we get the status code of each request:

![](https://imgur.com/cGGWmBJ.png)

> Note: I've used this site here to help me put together the regex above: [https://regexr.com/](https://regexr.com/)

Once you execute the create source query, you will have your source ready and you can view your sources by running the following query:

```
mz> SHOW SOURCES;
// Output
+-----------+
| name      |
|-----------|
| nginx_log |
+-----------+
SELECT 1
Time: 0.021s
```

Now that we have our source in place, let's go ahead and create a View!

# Creating a View

By using the `CREATE VIEW` statement, we can create **non-materialized views**.

What this essentially provides us with is more or less an alias for a `SELECT` statement.

For example, we know that we would want to select specific columns from the source that we've just created like the IP, the time, the request and etc. so rather than having to type the query each time, we could create a view, and reference it later on. You can think of it as an [alias command in Linux](https://devdojo.com/bobbyiliev/how-to-create-custom-bash-commands).

An important thing to keep in mind is that the standard views are very different from the Materialized views. In order to create a Materialized view, you can use the `CREATE MATERIALIZED VIEW` statement. We will review this in the next chapter of this tutorial.

To create our view we will use the following statement:

```
CREATE VIEW logs AS SELECT ipaddress,time,request,url,statuscode FROM nginx_log;
```

The important things to note are:

* First we start with the `CREATE VIEW logs` which identifies that we want to create a new view. The `logs` part is the name of our view, kind of like the name of a table.
* Then we specify the `SELECT` that we want the view to be based on. This could include complex `JOIN`s, but for the sake of this tutorial we are keeping things simple
* Finally we specify that we want to use the `nginx_log` source that we just created. 

With our standard SQL view in place, we are ready to move to the most exciting part, Materialized Views!

# Creating a Materialized View

Materialize lets you create materialized views that allow you to retrieve incrementally updated results of a SELECT query very quickly. You can think of the Materialized Views as the most powerful feature of Materialize.

In order to create a view, we will use the following command:

```
CREATE MATERIALIZED VIEW mz_logs AS SELECT * FROM logs;
```

The important things to note are:

* The moment you execute the statement, **Materialize creates a dataflow** to match the SQL 
* Then Materialize processes each line of the log through the dataflow, and keeps listening for new lines. This is incredibly powerful for dashboards that rely on real-time data.

A quick rundown of the statement itself:

*  First we start with the `CREATE MATERIALIZED VIEW mz_logs` which identifies that we want to create a new Materialized view. The `mz_logs` part is the name of our Materialized view, kind of like the name of a table.
 * Finally we specify that we want to use the `logs` standard SQL view that we just created.
 
When creating a Materialized View, it could be based on multiple sources like your Kafka Stream, a raw data file that you have on an S3 bucket, and your PostgreSQL database. This single view will give you the power to analyze your data in real-time.

> We specified a simple `SELECT` that we want the view to be based on but this could include complex `JOIN`s, however for the sake of this tutorial we are keeping things simple.

For more information about the Materialized Views check out the official documentation here:

[Creating Materialized views](https://materialize.com/docs/sql/create-materialized-view/)

Now you could use this new view and interact with the data from the Nginx log with pure SQL!

# Demo

Let's go ahead and create another view that would show us the number of total GET requests that we've received in our logs:

```
CREATE MATERIALIZED VIEW mz_logs_post_count AS SELECT COUNT(*) FROM logs where request='POST';
```

Now if you were to do a `SELECT` on this new Materialized view that we just created you will get the count of all POST requests in your log:

```
select * from mz_logs_post_count;
 count
-------
   362
(1 row)
```

Now if you were to create a while loop and start executing post requests, you will see the count number climb. Let's quickly check that with a simple `watch` command:

```
watch -n0.5 "psql -c 'select * from mz_logs_post_count' -U materialize -h localhost -p 6875 materialize"
```

Output:

![](https://imgur.com/tib10de.gif)

Next let's create a Materialized view where we will group our result based on how many times a specific IP shows up in the log, that way we can tell if a specific IP is making a suspicions number of requests:

```
CREATE MATERIALIZED VIEW mz_logs_ip_count AS select ipaddress, COUNT(*) as c FROM logs GROUP BY ipaddress;
```

Again if we were to use the `watch` command we could see the numbers change instantly as soon as we get new data in the log as Materialize processes each line of the log through the dataflow, and keeps listening for new lines:

![](https://imgur.com/niwnCOa.png)

Feel free to experiment with more complex queries and analyze your Nginx access log for suspicious activity using pure SQL and have the results with real-time data!

# Conclusion

In case that you like the project, make sure to star it on GitHub:

[https://github.com/MaterializeInc/materialize](https://github.com/MaterializeInc/materialize)

If you are totally new to SQL, make sure to check out this free eBook here:

[Free introduction to SQL basics eBook](https://github.com/bobbyiliev/introduction-to-sql)

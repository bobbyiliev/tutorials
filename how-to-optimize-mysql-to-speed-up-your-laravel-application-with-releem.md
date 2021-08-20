---

title: How to optimize MySQL to speed up your Laravel application with Releem?
tags: Laravel,releem,optimization,mysql
image: https://cdn.devdojo.com/posts/images/September2021/how-to-optimize-mysql-to-speed-up-your-laravel-application-with-releem3.jpg
status: published

---

# Introduction

Optimizing your MySQL service is a great way to improve your Laravel application's overall performance. 

Of course, it is important to follow Laravel Eloquent's best performance practices as described in [Jonathan Reinink](https://twitter.com/reinink)'s course on [Laravel Eloquent performance patterns](https://eloquent-course.reinink.ca).

But you should also not forget the server-side of things too. There are numerous MySQL server settings that you could change and tweak which could be crucial to both your performance and stability. 

In the past, you had to have a solid understanding and experience with MySQL performance tunning before you could jump into the MySQL config file and start changing things. It was a nightmare to make a mistake and completely crash a production database server.

Luckily thanks to [Roman Agabekov](https://twitter.com/agabekovroman) we now have Releem! 

Releem is the most effective way to improve MySQL performance and keep its configuration well-tuned using AI and best practices.

Releem is perfect for both people with less experience in server management and database performance tuning as well as experienced DBAs.

In this tutorial, you will learn how to use Releem to optimize your MySQL service and drastically boost the performance and reliability of your Laravel website!

# Prerequisites

In order for you to be able to follow along, you need to have the following things:

* A Laravel project up and running. If you don't have one yet or if you just want to follow along, you can follow the steps on [How to Install Laravel with 1-Click here](https://devdojo.com/bobbyiliev/how-to-install-laravel-on-digitalocean-with-1-click)!

 * You would also need to have a Releem account. You can get a free trial via this link here: [Releem free trial](https://releem.com/#rec221377760)

# Releem introduction

There are 4 main things that I would like to point out on how Releem works:

1. First we have the Releem Agent that automatically collects MySQL metrics, system information and transfers them to the Releem Cloud Platform

2. We have the Releem Cloud Platform analyzes that collected metrics, calculates MySQL Performance Score, and automatically detects performance issues

3. After that we have the Releem Cloud Platform makes recommendations, where we see the new recommended MySQL server configuration to improve performance

4. Finally we have the Releem Agent that applies the recommended configurations for us. You can apply the new configuration file manually or automatically

That way we can leave the whole process to Releem and focus on building our application and not worry about our MySQL performance degradation and optimization.

To learn more about Releem, make sure to check out this 1-minute video demo on how Releem's MySQL Performance Tuning as a Service works:

{% youtube 7gixIYTpuPU %}

# Installing Releem

Once you have your Releem account ready, follow these steps on how to install Releem on your server. Once installed, the Releem Agent will start to automatically collect metrics and recommend configuration.

### Installing the Releem agent

To install the agent run the following one-step install command:

```
RELEEM_API_KEY=YOUR_KEY_HERE bash -c "$(curl -L https://releem.s3.amazonaws.com/install.sh)" 
```

> Make sure to change the `YOUR_KEY_HERE` part with your actual Releem key!

### Configuring the Releem MySQL access

After that, in order for the agent to be able to connect to your MySQL service, create `~/.my.cnf` file with the following content:

```
[client] 
user=root
password=your_mysql_root_password_here
```

> Make sure to change the `your_mysql_root_password_here` part with your actual MySQL root password

For more information on how the above works, you could take a look at this tutorial here:

[How to securely login to MySQL without providing password each time?](https://devdojo.com/bobbyiliev/how-to-securely-login-to-mysql-without-providing-password-each-time)

### Running the Releem Agent manually

In order to send your very first metrics from your server to the Releem Cloud Platform, run the Releem Agent manually once:

```
/bin/bash /opt/releem/mysqlconfigurer.sh -k YOUR_RELEEM_KEY_HERE
```

> Don't forget to change the `YOUR_RELEEM_KEY_HERE` part with your actual Releem Key.

After this first execution, you will be able to see your current MySQL Performance Score for this specific server on the Metrics page of your Releem account.

### Configuring the Releem Agent cron job

In order to get better recommendations, don't forget to add the Releem Agent in your crontab.

First, open your crontab with the following command:

```
crontab -e
```

Then add the following line at the bottom:

```
10 */12 * * * PATH=/bin:/usb/bin:/usr/sbin /bin/bash /opt/releem/mysqlconfigurer.sh -k YOUR_RELEEM_KEY_HERE
```

By having this cron job in place, your Releem Agent will periodically collect new metrics and recommend performance-optimized configuration tailored for your server and website.

Releem Agent will automatically collect metrics and recommend configuration.

# Applying the recommended MySQL configuration

Once installed and configured, the Releem Agent will automatically store the recommended MySQL configuration at the following file:

```
/tmp/.mysqlconfigurer/z_aiops_mysql.cnf
```

In order to apply those recommended configuration settings, you can follow the steps here.

1. Copy Recommended Configuration to MySQL configuration folder:

```
cp /tmp/.mysqlconfigurer/z_aiops_mysql.cnf /etc/mysql/conf.d/ 
```

> For CentOS servers instead of `/etc/mysql/conf.d` you should use `/etc/my.cnf.d` 

After that, don't forget to restart the MySQL service in order to apply the new optimized configuration:

```
systemctl start mysql
```

With that, your MySQL service would use the MySQL configuration for optimal performance and reliability.

# MySQL Performance Benchmark

Here are some benchmarking results based on the configuration changes suggested by Releem:
---

### Read operations graph

![Read operations count for three MySQL configurations depending on threads count.](https://imgur.com/udVGZ6d.png)
---

### Write operations graph

![Write operation count for three MySQL configurations depending on threads count.](https://imgur.com/Q3rW4XY.png)
---

### Reads and writes counts 

![Reads and writes counts for three MySQL configurations with two threads](https://imgur.com/0eHqSUN.png)

# Conclusion

Optimizing your MySQL service on the server level using Releem is an excellent and quick way to boost the performance of your Laravel application without having to make any significant changes.

If you like Releem make sure to check it out here:

https://releem.com

The first 5 people to message me on Twitter would get a Releem free license:

[@bobbyiliev_](https://twitter.com)

I hope that this helps and if you have any questions make sure to put them in the comments below.

Source: [How to optimize MySQL to speed up your Laravel application with Releem?](https://devdojo.com/bobbyiliev/how-to-optimize-mysql-to-speed-up-your-laravel-application-with-releem)
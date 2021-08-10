---

title: How to keep a process running even after closing SSH connection?
tags: linux,ubuntu,devops,ssh,screen
image: https://cdn.devdojo.com/posts/images/September2021/how-to-keep-a-process-running-even-after-closing-ssh-connection.jpg
status: draft

---

# Introduction

There are many reasons why you would like to keep a process running even if you close your SSH session.

Here are a few examples:

* Your working day is going to be over soon and you are running a huge database import that's taking a long time to complete, you would not want to stay and wait for the import to complete but just hand over the task to the next person on shift
* You are downloading a huge file and it's going to take a good few hours, you would not want to leave your terminal open and wait, or even worse, you don't want to start all over again in case that your internet connection drops

# The `screen` command

What I usually do in such cases is to use the `screen` command and run the processes in a `screen` session. That way I could detach from the `screen` session and close my SSH connection and the process would still run allowing me or other people to attach to the session later on and follow up on the process.

Here's a quick introduction to how to use `screen`!

According to the official documentation `screen` is a full-screen window manager that multiplexes a physical terminal between several processes, typically interactive shells. When `screen` is called, it creates a single window with a shell in it where you could run commands as normal.

# Installation

I've noticed that in most cases screen is installed by default, so to check if you have screen installed you could run the following:

```
screen --version
```

If you get `command not found`, then you could install `screen` by running:

* For Ubuntu and Debian:

```
sudo apt update -y
sudo apt install screen
```

* On CentOS:

```
yum update -y
sudo yum install screen
```

# Usage

Once you have `screen` installed, to start a new `screen` session run:

```
screen -S SOME_NAME_HERE
```

This would spin up a new `screen` and you would be attached to it automatically, inside the screen session run your script. 

After that to detach from the screen session press `CTRL+a+d`.

If you need to attach back to the screen session run and check on your process just run:

```
screen -R SOME_NAME_HERE
```

If you've forgotten the name of your `screen` session or if you've not set a name, you could list all available `screen` sessions by running:

```
screen -ls
```

# Conclusion

This is just a really brief introduction on how to use `screen`, I would recommend checking out the official documentation as well:

https://www.gnu.org/software/screen/manual/screen.html

Hope that this helps!
---

title: What is a Fork Bomb in Linux and how to stop it?
tags: php,laravel,webdev
image: https://cdn.devdojo.com/posts/images/June2021/what-is-a-fork-bomb-in-linux-and-how-to-stop-it1.jpg
status: draft

---

# Introduction

A fork bomb (also known as a rabbit virus) is a denial-of-service attack that consists of a process that constantly replicates itself to exhaust all available system resources, slowing down or crashing the system due to resource starvation.

![What is a Fork Bomb in Linux and how to stop it?](https://imgur.com/wGtKsWk.png)

Here's an example of the most popular fork bomb in Linux:

```
:(){ :|:& };:
```

> NOTE: **do not run this on your system as it would crash the system**!

# Rundown of all elements

Here's a quick rundown of all elements:

* `:()` - Define the function. The `:` is the function name and the opening and closing parenthesis means that the function does not accept any arguments

* `{ }` - These characters show the beginning and end of the function

* `:|:` - Here it loads a copy of the function `:` into memory and pipe its own  output to another copy of the `:` function, which has to be loaded into memory as well

* `&` - This starts the process as a background process

* `:` - The final `:` executes the function and hence the chain reaction begins

# Stopping a fork bomb 

If you have a multi-user system, the best way to protect it against such attacks is to limit the number of processes a user can have by using PAM for example.

If you are already logged into the system you could do the following to stop the fork bomb:

* Run a SIGSTOP command to stop the processes of the user who ran the fork bomb: 

```
killall -STOP -u someuser
```

# Conclusion

For more information about the history of the fork bomb and other examples I would recommend checking this [Wikipedia page](https://en.wikipedia.org/wiki/Fork_bomb).
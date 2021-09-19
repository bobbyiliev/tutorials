---

title: 17 Special Characters in the Shell That You Should Know
tags: shell,bash,linux,devops
image: https://cdn.devdojo.com/posts/images/May2021/17-special-characters-in-the-shell-that-you-should-know.jpg
status: published

---

# Introduction

In Unix systems, the [shell](https://en.wikipedia.org/wiki/Unix_shell) is a command-line interpreter. It provides a command-line user interface (CLI). The shell is a scripting language that you could use to write scripts like any other programming language and it is also an interactive command language. 

It is used by the operating system to control the execution of the system using shell scripts.

---

# Prerequisites

In order to be able to follow along and test the examples by yourself, you would need a Unix terminal. If you are using Linux or Mac you can just use the built in terminals. 

If you are on Windows you would need a Linux virtual machine or you could use DigitalOcean and spin up an Ubuntu Droplet for example and test the commands directly on the server:

[DigitalOcean $100 Free Credit](https://m.do.co/c/2a9bba940f39)

---

# List of special characters

There are a lot of characters that have special meaning to the shell.

Here are 17 of those special characters in the Shell with examples on how to use them.

# 1. The `~` symbol
You can use the `~` symbol as a shortcut to your home directory:
```
~
```

For example, if you want to edit a file which is stored at your home directory, rather than typing the full path to the file, you could just use the `~` symbol:

```
vim ~/.bashrc
```

# 2. The `\` symbol
Like in a lot of other programming languages you can use the `\` symbol to escape characters.

```
\
```

Here's a short script as an example:

```
#!/bin/bash

my_var="hello world"

echo $my_var
echo ""
echo \$my_var
```

Here we first define a variable called `my_var` and we assigned "hello world" as the value. The first echo would output "hello world" but the second one would just output `$my_var` as we are using the back slash to escape the $ symbol which essentially means that it is no longer a variable. Give this a go and see the result!

# 3. The `/` symbol

Unlike Windows where you would use backslashes for paths, in Unix systems you would use forward slashes:

```
/
```

For example if you wanted to go to a directory called logs located in your user's home directory you could use the following:

```
cd /home/bobby/logs
```

# 4. The `$` symbol

The `$` symbol precedes any variable similar to PHP and some other languages.

For example:

```
my_var="Hello World"
echo $my_variable
```

# 5. The `?` symbol

The `?` symbol is a single-character wildcard, you can use this if you are uncertain of 1 specific character. 

Let's say that you have a directory that contains the following files: top, tap, tip, bip, tips. If you run the following command:

```
ls t?p
```

This would match all of the words that are 3 letters long and start with the letter t and end with the letter `p`.

# 6. The `'` symbol

Similar to other languages, the single quotation mark ('), escapes any special characters. For example if you run the following command:

```
echo 'I found $100!'
```

In this case the `$100` would not be interpreted as a variable but just a normal string.

# 7. The ` symbol

The backtick (`) allows for evaluating a string as part of general command, for example, if you run the following command:

```
echo "Today is `date`"
```

The date command would actually be executed and you would get the following output:

```
Today is Sun May 31 13:55:10 UTC 2020
```

# 8. The `"` symbol
As you probably expect, with the double quotation marks, unlike the single quotation marks, the special characters between the quotes would be executed as normal rather than being escaped.

For example:

```
name="Bobby"
echo "Hi there $name"
```

In this case the output that you would get would be:

```
Hi there Bobby
```

# 9. The `*` symbol

The `*` symbol is a wildcard symbol and is very widely used. 

It matches zero or more characters, for example, if you have a directory with a lot of files ending in `.txt`, in order to get a list of all of those files you could run:

```
ls -l *.txt
```

# 10. The `&` symbol

The `&` symbol is used to start a process in the background, for example:

```
redshift &
```

The above would start the redshift process in the background so that you could continue using your current shell session.

# 11. The `&&` symbol

Similar to all other programming languages, `&&` lets you do something based on whether the previous command completed successfully or not. 

That's why you tend to see it chained like this:

```
 command1 && command2
```

The above means that if command1 returns true, then do command2.

# 12. The `||` symbol

The `||` is an `or` comparison operator like with any other programing language.

For example, if command1 returns false, then do command2:

```
command1 || command2 
```

# 13. The `|` symbol

The `|` symbol denotes a pipe.  A Pipe is a command in Linux that lets you use two or more commands together, by passing the output of one command as the input to the next. 

Example:

```
ls –l | grep my-file.txt
```

The above would pass the output of the `ls -l` command to grep which would then search for a file called `my-file.txt`.

# 14. The `;` symbol

By using the `;` symbol you can execute multiple commands on one line and you can use the `;` symbol instead of a new line.

For example, you could take this for loop here:

```
for i in {1..10}
do
    echo $i
done
```

And convert it to a 1 liner like this:

```
for i in {1..10}; do echo $i ; done
```

# 15. The `[]` symbol

As part of a regular expression, brackets define a range of characters to match. For example, let's say that we have a directory and you would like to get a list of all files that contain a number in their name, what you could do is use the following:

```
ls | grep [0-9]
```

# 16. The `>` symbol

The `>` symbol is used to redirect output of a command to a file, for example:

```
echo "Hello world" > /tmp/world.txt
```

The above would redirect the output of the echo command to a file called `world.txt` in the `/tmp` folder. If the file does not exist it would be created automatically.

# 17. The `<` symbol

The `<` symbol is used to redirect input to a program. For example, if you have a `.sql` file, you could import it to your MySQL server with the following command:

```
mysql my_database < my_backup.sql
```
---

# Conclusion

Once you know a good number of those you could then start using them together and do lots of cool scripts!

I hope that this helps!

Source: [Initial 17 Special Characters in the Shell That You Should Know](https://devdojo.com/bobbyiliev/17-special-characters-in-the-shell-that-you-should-know)
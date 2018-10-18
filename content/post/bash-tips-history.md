---
title: "Bash Tips: History"
date: 2018-10-18T10:03:46-04:00
draft: true
author: "Victor Mendonça"
description: "Tips for managing history in Bash"
tags: ["Linux", "Bash"]
---

Event Designators (`!`, `!!`, `!*`)
---

**From MAN Pages**

> An event designator is a reference to a command  line  entry  in  the  history  list. Unless  the reference is absolute, events are relative to the current position in the history list.

For the different event designators below, assume we are running it against the same command (`netstat -an | grep ':22'`). This is to help you visualize the differences between each of the event designators.

### **Repeating the last command that starts with `!string`**

You can run the most recent command starting with `string` by using `!string`.

```bash
$ !netstat
netstat -an | grep ':22'
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp6       0      0 :::22000                :::*                    LISTEN     
tcp6       0      0 :::22
```

_**Another example:**_

```bash
$ ls my_file.txt
.rw-r--r--     0 victor users 18 Oct 11:35  my_file.txt

$ ls my_folder
drwxr-xr-x     - victor users 18 Oct 11:35  my_folder

$ !ls
ls my_folder
drwxr-xr-x     - victor users 18 Oct 11:35  my_folder
```

### **Repeating the same command with `!!`**

As you may already know, the `!!` can be used for repeating the same command.

```bash
$ echo !!
echo netstat -an | grep ':22'
```

This is useful when you forget to use `sudo` on a command that requires sudo access.

_**For example:**_

```bash
$ systemctl daemon-reload
Failed to reload daemon: Access denied

$ sudo !!
sudo systemctl daemon-reload
[sudo] password for victor:
```

### **Re-using the last argument with `!$`**

Another very useful option is the `!$`, which provides quick access to the last argument in a command.

```bash
$ echo !$
echo ':22'
```

This can come in handy if you need to repeat another command to the same last argument.

```bash
$ touch my_new_script.sh

$ vim !$
vim my_new_script.sh
```


### **Editing the last command with `fc`**

The command `fc` allows you to open commands in your history with an editor, modify the commands and then execute the modified version.

fc -20 -1


History Settings
---

The next

**Adds time to each command in history**

```bash
# Sets up time for history
export HISTTIMEFORMAT="+%Y/%m/%d %T "
```

```bash
# Avoid duplicates
export HISTCONTROL=ignoredups:erasedups
```

```bash
# When the shell exits, append to the history file instead of overwriting it
shopt -s histappend
```

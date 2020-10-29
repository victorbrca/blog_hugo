---
title: "Bash Tips: History"
date: 2019-03-11T18:30:00-04:00
draft: false
author: "Victor Mendonça"
description: "Tips for managing history in Bash"
tags: ["Linux", "Bash"]
---

Bash keeps a history of the commands you type in and saves them to `~/.bash_history`. These commands can be accessed in many ways and there are a lot of configuration related on how they are saved. I'll cover some basic functionality here.

Event Designators (`!`, `!!`, `!*`)
---

**From MAN Pages**

> An event designator is a reference to a command  line  entry  in  the  history  list. Unless  the reference is absolute, events are relative to the current position in the history list.

For the different examples below, assume we are running it against the same command (`netstat -an | grep ':22'`). This is to help you visualize the differences between each of the event designators.

### **Repeating the last command that starts with `!string`**

You can run the most recent command starting with `string` by using `!string`.

```none
$ !netstat
netstat -an | grep ':22'
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp6       0      0 :::22000                :::*                    LISTEN     
tcp6       0      0 :::22
```

_**Another example:**_

```none
$ ls my_file.txt
.rw-r--r--     0 victor users 18 Oct 11:35  my_file.txt

$ ls my_folder
drwxr-xr-x     - victor users 18 Oct 11:35  my_folder

$ !ls
ls my_folder
drwxr-xr-x     - victor users 18 Oct 11:35  my_folder
```

**_Tip:_** _you can use the `:p` option to print the command instead of running it_

```none
$ !netstat:p
netstat -an | grep ':22'
```

### **Repeating the same command with `!!`**

As you may already know, the `!!` can be used for repeating the same command.

```none
$ echo !!
echo netstat -an | grep ':22'
```

This is useful when you forget to use `sudo` on a command that requires sudo access.

_**For example:**_

```none
$ systemctl daemon-reload
Failed to reload daemon: Access denied

$ sudo !!
sudo systemctl daemon-reload
[sudo] password for victor:
```

### **Re-using the last argument with `!$`**

Another very useful option is the `!$`, which provides quick access to the last argument in a command.

```none
$ echo !$
echo ':22'
```

This can come in handy if you need to repeat the last argument with another command.

```none
$ touch my_new_script.sh

$ vim !$
vim my_new_script.sh
```

_**Another example:**_

```none
$ mkdir myfolder

$ cd !$
```

The `fc` command
---

The command `fc` allows you to open commands in your history with an editor, modify the commands and then execute the modified version. It will use your default editor (as per `$EDITOR`), so make sure that is set.

Some common usage for `fc`:

* `fc -l` - List your last commands
* `fc n` - Edit the _n_ command
* `fc -1` - Edit the last command
* `fc 20 22` - Edit commands 20 to 22


History Settings
---

On this section we talk a little bit about some of the configuration available for your Bash history.

### **Adds time to each command in history**

```bash
# Sets up time for history
export HISTTIMEFORMAT="+%Y/%m/%d %T "
```

_**For example:**_

```none
$ history | tail -n 10
  949  +2019/03/11 00:32:53 ssh seedbox
  950  +2019/03/11 16:05:53 cd .config/polybar
  951  +2019/03/11 16:05:56 ./launch.sh
  952  +2019/03/11 17:49:38 lolbanner victor
  953  +2019/03/11 17:53:10 lolbanner test
  954  +2019/03/11 17:53:11 psg checkupdates
  955  +2019/03/11 17:53:11 clear
  956  +2019/03/11 17:53:11 echo hello 1
  957  +2019/03/11 17:53:11 history
  958  +2019/03/11 18:10:01 history | tail -n 10
```

### **Avoid duplicate entries**

```bash
# Avoid duplicates
export HISTCONTROL=ignoredups:erasedups
```

### **Append entries**

This comes in handy if you run multiple terminal emulators simultaneously. By default, the latest closed terminal emulator will overwrite history from the other windows. With this setting, changes are appended making sure all commands are saved to history.

```bash
# When the shell exits, append to the history file instead of overwriting it
shopt -s histappend
```

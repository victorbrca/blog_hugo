---
title: "How to View DNS Calls Made by Processes"
date: 2017-10-06T18:46:20-04:00
draft: false
author: "Victor Mendonça"
description: "Quick instructions on how to capture application DNS requests on Linux."
tags: ["Linux", "Network"]
---

We had the need at work to monitor DNS calls made by an application in a RHEL system in order to stabilish if a connection pool config change had taken full effect, or if we had missed any configuration file. And the solution was to use `SystemTap` for this task.

[SystemTap (stap)](https://en.wikipedia.org/wiki/SystemTap) is a scripting language and tool that simplifies the gathering of information about the running Linux system. It allows you to monitor and trace the operation of a Linux kernel.

It should be present on most RHEL server installs, but for other desktop based distros (like Arch), you might need to install it.

## Overview

The image below shows you the sript working and recording DNS lookup from Firefox (note the difference on the amount of connections between Google/Yahoo vs DuckDuckGo).

![](https://imgur.com/i7optIs.gif)

## Installation

Install `systemtap` and `linux-headers`:

```
pacman -Syu systemtap linux-headers
```

Initial Config and Test
---

Because we are trying to figure out how the application makes DNS calls, we need to find where our `libc6` library lives so we can probe it for requests (most likelly `/usr/lib/libc.so.6` for Arch).

```
pacman -Ql glibc | grep '/libc.*so'
glibc /usr/lib/libc-2.26.so
glibc /usr/lib/libc.so
------------------------
glibc /usr/lib/libc.so.6
------------------------
glibc /usr/lib/libcidn-2.26.so
glibc /usr/lib/libcidn.so
glibc /usr/lib/libcidn.so.1
glibc /usr/lib/libcrypt-2.26.so
glibc /usr/lib/libcrypt.so
glibc /usr/lib/libcrypt.so.1
```

To test it, make sure the string `probe process("/usr/lib/libc.so.6")` has the location for libc6 on your system and run the command below:

```
sudo /usr/bin/stap -e 'probe process("/usr/lib/libc.so.6").function("getaddrinfo") { log(execname()) }'
```

You may get the following warning when running the script:

> WARNING: Kernel function symbol table missing [man warning::symbols]

This is because Systemtap may need a linux-build style System.map file to find addresses of kernel functions/data. Try the command below to create it by hand:

```
sudo cp /proc/kallsyms /boot/System.map-`uname -r`
```

## Running It
---

You can run the script below (or just the code) as `root` to monitor the connections. Output will be displayed on your current terminal, or you can choose to save it to a file.

```
_#!/bin/bash


system_stap="/usr/bin/stap"

_getaddrInfo () {
  /usr/bin/stap -e 'probe process("/usr/lib/libc.so.6").function("getaddrinfo")
{
  printf("| %-15s| %-7d| %-35s |\n", execname(), pid(), kernel_string(pointer_arg(1)))
}'
}

echo ""
printf "| %-15s| %-7s| %-35s |\n" "Process" "PID" "Destination Name"
echo "|----------------|--------|-------------------------------------|"


while true ; do
  _getaddrInfo
done
```

```
probe
  process("/lib64/libc.so.6").function("__gethostbyname_r").call,
  process("/lib64/libc.so.6").function("gethostbyname").call,
  process("/lib64/libc.so.6").function("__gethostbyname2_r").call,
  process("/lib64/libc.so.6").function("gethostbyname2").call,
  process("/lib64/libc.so.6").function("__new_gethostbyname2_r").call
{
	printf("[%s][%d]->%s(%s)\n", execname(), pid(), user_string(pointer_arg(1)), kernel_string(pointer_arg(1)))
}

```
Output:

```
$ sudo ./getAdreessInfo.sh
[sudo] password for victor:           

| Process        | PID    | Destination Name                    |
|----------------|--------|-------------------------------------|
| WorkerPool/5863| 5104   | adservice.google.ca                 |
| WorkerPool/6005| 5104   | apis.google.com                     |
| WorkerPool/1335| 5104   | clients5.google.com                 |
| WorkerPool/5823| 5104   | notifications.google.com            |
| WorkerPool/1856| 5104   | lh3.googleusercontent.com           |
| WorkerPool/5297| 5104   | ogs.google.com                      |
| WorkerPool/5823| 5104   | www.google.com                      |
| WorkerPool/1335| 5104   | www.gstatic.com                     |
| WorkerPool/5863| 5104   | uaswitcher.org                      |
| WorkerPool/6005| 5104   | fonts.gstatic.com                   |
| WorkerPool/5823| 5104   | play.google.com                     |
```

## Closing Notes
---

The uses for `SystemTap` are very wide and diverse. You will be able to find great tutorials and documentation online. I'm also providing a few links below to some great documentation to get you started.

* Red Hat Enterprise - [SystemTap Beginners Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/SystemTap_Beginners_Guide/index.html)
* Arch Linux Wiki - [SystemTap](https://wiki.archlinux.org/index.php/SystemTap)
* IBM Developerworks - [Linux introspection and SystemTap](https://www.ibm.com/developerworks/library/l-systemtap/index.html)
* Ubuntu Wiki - [SystemTap](https://wiki.ubuntu.com/Kernel/Systemtap)

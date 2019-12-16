---
title: "Bash Arrays and Iterations"
date: 2019-12-16T09:57:18-05:00
draft: false
author: "Victor Mendonça"
description: "Using Bash arrays for iterations"
tags: ["Bash", "Linux"]
---

Bash arrays can be great for iterating over a list of items.  I'm giving a quick example below on a list of services. All you need is to list all values in different arrays and use an index to map them back together.

Example:

|#|Service|Path|Config File|
|:-:|:---:|:---:|:---:|
|1|CUPS|/etc/cups|cupsd.conf|
|2|MPD|/etc|mpd.conf|
|3|SSHD|/etc/ssh|sshd.conf|
|4|DHCPD|/etc|dhcpd.conf|

Let's break down our columns into arrays:

```bash
service_arr=(
  CUPS
  MPD
  SSHD
  DHCPD
)

path_arr=(
  /etc/cups
  /etc
  /etc
  /etc
)

config_file_arr=(
  cupsd.conf
  mpd.conf
  sshd.conf
  dhcpd.conf
)
```

Now to iterate through our items, let's make sure we start at index 1 and add at each run:

```
cnt=1

for i in ${!service_arr[@]} ; do
  echo "The configuration file for the service ${service_arr[$i]} is ${path_arr[$i]}/${config_file_arr[$i]}"
  let count+=1
done
```

The result is:

```
The configuration file for the service CUPS is /etc/cups/cupsd.conf
The configuration file for the service MPD is /etc/mpd.conf
The configuration file for the service SSHD is /etc/sshd.conf
The configuration file for the service DHCPD is /etc/dhcpd.conf
```

You can also use especial characters (like `*` when searching for files and using shell expansion). Just make sure to properly quote them.


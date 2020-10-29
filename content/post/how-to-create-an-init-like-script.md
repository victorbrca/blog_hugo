---
title: "How to Create an Init Like Script"
date: 2018-09-17T11:40:47-04:00
draft: false
author: "Victor Mendonça"
description: "How to create an init like script to run in the background as a service"
tags: ["Bash", "Linux"]
---

On this tutorial I will explain how to create a quick init like script to be run in the background. We will **not** be adding this script to `/etc/init` or look into how to run it at startup. Instead we will run it manually. If you are looking for a Systemd version of this tutorial, check out my previous post [Creating a Simple Systemd User Service](https://blog.victormendonca.com/2018/05/14/creating-a-simple-systemd-user-service/).

First let's create our service script. This is the daemon that will be running in the background. For this example we will create a script that monitors a log file:

```
tail -fn0 logfile | \
while read line ; do
  echo "$line" | grep "pattern"
  if [ $? = 0 ]
  then
    ... do something ...
  fi
done
```

Now let's create a control script. This script is what we will use to start/stop our daemon.

```
#!/bin/bash

daemon="[path_to_my_daemon_script]"
name="Name for the service/daemon"
desc="Description for the script"
pid_file="/var/run/[daemon_name].pid"

# Check whether the binary is still present:
test -x "$daemon" || exit 0

case "$1" in
start)
  [ -f "$pid_file" ] && { echo "Already running" ; exit 0 ; }
  echo "Starting $name"
  "$daemon" &
  echo $! > "$pid_file"
  ;;
stop)
  [ ! -f "$pid_file" ] && { echo "Not running" ; exit 0 ; }
   echo "Stopping  $name"
   kill "$(cat $pid_file)"
   rm "$pid_file"
   ;;
restart)
   $0 stop
   $0 start
   ;;
status)
   if [ -e "$pid_file" ]; then
      echo "$name is running, pid=$(cat $pid_file)"
   else
      echo "$name is not running"
      exit 1
   fi
   ;;
*)
   echo "Usage: $0 {start|stop|status|restart}"
esac

exit 0
```

Make sure both files are executable and you are ready to start your daemon.

```
[controlscript] start
```

You can check the status, stop, etc...

```
[controlscript] status
```

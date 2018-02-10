---
title: "How to Create an Init Like Script"
date: 2018-01-31T23:18:11-05:00
draft: true
author: "Victor Mendonça"
description: "How to create an init like script to run in the background as a service"
tags: ["Bash", "Linux"]
---

On this tutorial I will explain how to create a quick init like script to be run in the background. We will **not** be adding this script to `/etc/init` or look into how to run it at startup. Instead we will run it manually. But we will be looking at how to add it as a system service later.

First, let's create our script. It can be anything, but for this example we will create a script that monitors a log file

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

---
title: "How to Create a Timer in Bash"
date: 2018-07-19T09:45:45-04:00
draft: false
author: "Victor Mendonça"
description: "How to create a timer (countdown) in Bash using the built-in `$SECONDS` variable"
tags: ["Linux", "Bash"]
---

Following my previous post on "[How to Create a Prompt With Timeout in Bash ](https://blog.victormendonca.com/2016/10/19/how-to-create-a-prompt-with-timeout-in-bash/)", we will now see how to create a timer (countdown) in Bash using the built-in `$SECONDS` variable.

The `$SECONDS` variable holds in the number of seconds the script has been running. So it can easily be used to create a timer inside your script in Bash.

```
$ bash -c 'while true ; do echo -en "\r${SECONDS}s elapsed" ; sleep 1 ; done'
203s elapsed
```

![Imgur](https://imgur.com//KZAso8o.gif)

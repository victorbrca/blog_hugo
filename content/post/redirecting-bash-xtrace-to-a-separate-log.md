---
title: "Redirecting Bash Xtrace to a Separate Log"
date: 2016-12-23T14:58:09-04:00
draft: false
author: "Victor Mendonça"
description: "How to redirect stderr to a separate log in a Bash script."
tags: ["Linux", "Bash"]
---


So you have a Bash script that you want to troubleshoot, but you want to send stdout to a file, and stderr to another. Here's a solution.

For example, I like to use Bash's color to display failure or success on checks, and echo's removal of new lines (`echo -e "Wait for it...\c "`) to wait for checks. For example, the screenshot below shows a script that check each step and report back.

![terminal](https://i.imgur.com/H3Bm6s7.png)

Errors are displayed in red, and success in green, and this same output is sent to the stdout and to a log file as well.

Now I want to get error messages in a separate log file, so I can go back to my original log and match the time where it failed, with additional verbose from Bash's xtrace.

The solution is very simple... I can just add the lines below to the top of my script:

```bash
# Debugging
debug_log="${HOME}/bin/log/script.debug"
exec 2>>${debug_log}
set -x
```

The first line setups my debug log, the second redirects stderr to my debug log, and the third enables xtrace (same as `bash -x [script]`).

Note that doing this will disable any errors from being show in stdout.

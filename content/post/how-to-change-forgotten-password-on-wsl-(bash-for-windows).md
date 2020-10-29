---
title: "How to Change Forgotten Password on WSL (Bash for Windows)"
date: 2018-07-04T18:48:26-04:00
draft: false
author: "Victor Mendonça"
description: "How to Change Forgotten Password on WSL (Bash for Windows)"
tags: ["Linux", "Bash", "Windows"]
---

So you have installed Bash for Windows, but forgot your password!! That's easy to fix, and here's how:

a. Find your username by running Bash for Windows and executing `whoami`

```
$ whoami
victor_m
```

b. Change the default user to `root` by running the code below on a Windows command prompt (cmd.exe)

```
LxRun.exe /setdefaultuser root
```

c. Now change the password with `bash -c 'passwd [user]'` (also on the Windows command prompt)

```
bash -c 'passwd victor_m'
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

d. Change the default user back to your user

```
LxRun.exe /setdefaultuser victor_m
```

e. Profit

---
title: "TLDR Instead of MAN"
date: 2018-10-16T08:49:07-04:00
draft: false
author: "Victor Mendonça"
description: "Using tldr instead of the man pages"
tags: ["Bash", "Linux"]
---

Here's a quick way to get quick simplified explanation and usage for commands in Bash with [TLDR Pages](https://tldr.sh/).

```
$ tldr tldr
# tldr                                                                            

  Simplified man pages.                                                           

- Get typical usages of a command (hint: this is how you got here!):              

  tldr command       
```

![](../img/tldr-instead-of-man/tldr.rsync.png)

TLDR pages is community driven and holds common commands for UNIX, Linux, macOS, SunOS and Windows. The amount of commands available is already pretty vast, and users are encouraged to contribute with new pages on their git repo - https://github.com/tldr-pages/tldr.

You can also access a web/live version of tldr on https://tldr.ostera.io/.


Install
---

**Arch**

```bash
pacman -Sy community/tldr
```

**Other distros:**

```bash
npm install -g tldr
```

**Snap**

```bash
sudo snap install tldr
```

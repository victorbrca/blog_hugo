---
title: "Quickly Backing Up Files in Bash"
date: 2018-10-15T14:18:36-04:00
draft: false
author: "Victor Mendonça"
description: "Quickly back up files in Bash"
tags: ["Linux", "bash"]
---

Here's a quick and simple way to backup files in Bash by using Bash's built-in brace expansion `{,}`.

Let's first create a file:

```bash
$ ls -l > listing.txt
```

Now let's create a backup:

```bash
$ cp listing.txt{,.bak}
```

And the result is a new file with the `.bak` extension:

```bash
$ ls -l listing*
Permissions Size User   Group Date Modified Name
.rw-r--r--  2.5k victor users 15 Oct 14:21  listing.txt
.rw-r--r--  2.5k victor users 15 Oct 14:21  listing.txt.bak
```

How about getting fancy and adding a date?

```bash
cp listing.txt{,.$(date +%Y%m%d_%H%M)}
```

And the result:

```bash
$ ls -l listing.tx*
Permissions Size User   Group Date Modified Name
.rw-r--r--  2.5k victor users 15 Oct 14:21  listing.txt
.rw-r--r--  2.5k victor users 15 Oct 14:21  listing.txt.bak
.rw-r--r--  2.5k victor users 15 Oct 14:23  listing.txt.20181015_1423
```

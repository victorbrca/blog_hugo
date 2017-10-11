+++
title = "Additional Status for Vim With Powerline"
date = 2017-09-20T13:26:46-04:00
draft = false
author = "Victor Mendonça"
tags = ["vim", "bash", "Linux"]
+++

Would you like to have more information displayed while reading files in VIM? [Powerline](https://powerline.readthedocs.io/en/latest/index.html) is a great utility for that.

![Imgur](https://i.imgur.com/0vCK9an.png)

In it's default config, it displays:

* Current mode (normal, insert, visual)
* Git branch
* File name
* File encoding
* Script type
* File view percentage
* Line number

To install it
---

### Arch:

```bash
sudo pacman -Ss python-powerline powerline
```

Add the line below to your `~/.vimrc`

```bash
set laststatus=2
```

* * *

**Note:** If you get the error below

```
Traceback (most recent call last):
  File "<string>", line 9, in <module>
ImportError: No module named powerline.vim
An error occurred while importing powerline module.
This could be caused by invalid sys.path setting,
or by an incompatible Python version (powerline requires
Python 2.6, 2.7 or 3.2 and later to work). Please consult
the troubleshooting section in the documentation for
possible solutions.
If powerline on your system is installed for python 3 only you
should set g:powerline_pycmd to "py3" to make it load correctly.
Unable to import powerline, is it installed?
Press ENTER or type command to continue
```

Modify either `~/.vimrc` (or `/etc/vimrc` if you want the fix available for multiple users) by adding the line below:

```vim
let g:powerline_pycmd = 'py3'
```

### Ubuntu 16.04

Use Python 3

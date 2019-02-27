---
title: "Fzf for the Win"
date: 2019-02-27T08:00:39-04:00
draft: false
author: "Victor Mendonça"
description: "fzf is a command line fuzzy finder"
tags: ["Linux", "Bash", "fzf"]
---

`fzf` is a command line fuzzy finder that can be used to automatically filter a list of items. Think of it as an interactive search tool, where items get filtered as you type characters in your terminal.

The video below shows a basic interaction using a list or files from the `fd` search utility:

![](../img/fzf-for-the-win/fzf.gif)

`fzf` can also be used with other Bash tasks, like history, ssh and even file/dir completion. The GitHub page has a lot documentation on how to implement auto completion.  

![](../img/fzf-for-the-win/history-and-ssh.gif)

You can also use the `--preview` option to output the current selection into a preview box, and even call a command to be used with that value. For example, we can preview all the files in a folder by searching for files (with `find` or `fd`), piping the output to `fzf`, and then using a program like `cat` (on the example below I'm using `bat`, which is a clone of `cat` with the addition of syntax highlight and other cool things) to preview the files.

```
fd -d 1 -t f | fzf --preview 'bat --color "always" {}' --preview-window=right:60%
```

![](../img/fzf-for-the-win/preview.gif)

I've covered only the very basic usage for `fzf`, but it should give you an idea of how powerful this finder utility is. On future posts I'm going to cover other use cases, like the git workflow that I use.

* * *

**References:**

* fzf - https://github.com/junegunn/fzf
* fd  - https://github.com/sharkdp/fd
* bat - https://github.com/sharkdp/bat

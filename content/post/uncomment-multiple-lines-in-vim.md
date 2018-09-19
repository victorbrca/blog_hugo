---
title: "Editing Multiple Lines in Vim"
date: 2018-09-19T10:41:47-04:00
draft: false
author: "Victor Mendonça"
description: "How to edit multiple lines in VIM"
tags: ["Bash", "Linux", "Vim"]
---

A simple way of to edit (like commenting or uncommenting) a block of lines/code in Vim.

The example below explains how to comment multiple lines:

* Place the cursor on the first line that you'd like to edit
* Press `Ctrl+v`
* User the arrow keys to go down until the last line
* Press `Shift+i` to go into insert mode
* Press `#`
* Press `Esc` and wait a second

![](/img/uncomment-multiple-lines-in-vim.gif)

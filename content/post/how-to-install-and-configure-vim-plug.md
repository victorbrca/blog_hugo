---
title: "How to Install and Configure Vim Plug"
date: 2018-12-31T13:44:17-05:00
draft: false
author: "Victor Mendonça"
description: "Step by step instructions for installing vim-plug"
tags: ["vim", "bash", "Linux"]
---

Quick step by step instructions to get you started with the Vim plugin manager [vim-plug](https://github.com/junegunn/vim-plug).

a- Install the `vim-plug` package (Arch) from the AUR repo

```
aur/vim-plug 0.10.0-1 [installed] (19) (0.05)
    A vim plugin manager
```

b. Configure your `~/.vimrc` with a section for the new plugins. This is where you will define all the plugins that you want to use. I would also add this section at the top of your file.

```
"==============================================================================
" Plugin Manager
"==============================================================================

call plug#begin('~/.vim/plugged')

" Initialize plugin system
call plug#end()
```

c. Add a plugin to the plugin section. If you don't know what to add you can start with `vim-illuminate`, which highlights multiple instances of the same word that is under the cursor.

```
call plug#begin('~/.vim/plugged')

"Vim plugin for selectively illuminating other uses of the current word under the cursor
"https://github.com/RRethy/vim-illuminate
Plug 'RRethy/vim-illuminate'

" Initialize plugin system
call plug#end()
```

d. Now open a file with vim, or if you already have a file open, source your `vimrc` with `:source ~/.vimrc`

e. Now that we have the plugin section and a plugin defined, call vim-plug to install your plugin with `:PlugInstall`

And that's it! You are ready to start using your new plugin.

Whenever you want to install a plugin, repeat steps "c" through "e".

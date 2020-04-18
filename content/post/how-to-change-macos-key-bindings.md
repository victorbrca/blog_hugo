---
title: "How to Change Macos Key Bindings"
date: 2020-04-14T12:19:14-04:00
draft: true
author: "Victor Mendonça"
description: ""
tags: ["macOS", "VirtualBox"]
---

https://gist.github.com/trusktr/1e5e516df4e8032cbc3d


```
mkdir ~/Library/KeyBindings/
vim DefaultKeyBinding.Dict
```

```
{
    /* Ctrl + Left */
    /* "^\UF702"  = "moveWordLeft:"; */
    "@\UF702"  = "moveWordLeft:";

    /* Ctrl + Right */
    /* "^\UF703"  = "moveWordRight:"; */
    "@\UF703"  = "moveWordRight:";

    /* Ctrl + Shift + Left */
    /* "^$\UF702" = "moveWordLeftAndModifySelection:";*/
    "@$\UF702" = "moveWordLeftAndModifySelection:";

    /* Ctrl + Shift + Right */
    /* "^$\UF703" = "moveWordRightAndModifySelection:";*/
    "@$\UF703" = "moveWordRightAndModifySelection:";

    /* Remap Home / End keys */
    /* Home Button*/
    "\UF729" = "moveToBeginningOfLine:";
    /* End Button */
    "\UF72B" = "moveToEndOfLine:";

    /* Shift + Home Button */
    "$\UF729" = "moveToBeginningOfLineAndModifySelection:";

    /* Shift + End Button */
    "$\UF72B" = "moveToEndOfLineAndModifySelection:";

    /* Ctrl + Home Button */
    /* "^\UF729" = "moveToBeginningOfDocument:"; */
    "@\UF729" = "moveToBeginningOfDocument:";

    /* Ctrl + End Button */
    /* "^\UF72B" = "moveToEndOfDocument:"; */
    "@\UF72B" = "moveToEndOfDocument:";

    /* Shift + Ctrl + Home Button */
    /* "$^\UF729" = "moveToBeginningOfDocumentAndModifySelection:"; */
    "$@\UF729" = "moveToBeginningOfDocumentAndModifySelection:";

    /* Shift + Ctrl + End Button*/
    /* "$^\UF72B" = "moveToEndOfDocumentAndModifySelection:"; */
    "$@\UF72B" = "moveToEndOfDocumentAndModifySelection:";
}
```

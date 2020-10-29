---
title: "How to Change macOS Key Bindings"
date: 2020-04-27T13:45:00-04:00
draft: false
author: "Victor Mendonça"
description: "How to change macOS line movement keyboard shortcuts"
tags: ["macOS", "VirtualBox"]
---

This post will show you how to change line movement and control key bindings in macOS to be similar to what we use in Linux (and windows).

The following keyboard shortcuts will be added:

Sequence | Command |
|:-:|:---:|
Ctrl+Left | Back one word |
Ctrl+Right | Forward one word |
Ctrl+Shift+Left | Back one word and modify selection |
Ctrl+Shift+Right | Forward one word and modify selection |
Home | Beginning of the line |
End | End of line |
Shift+Home | Beginning of the line and modify selection |
Shift+End | End of line and modify selection |
Ctrl+Home | Top of page |
Ctrl+End | End of page |
Shift+Ctrl+Home | Top of page and modify selection |
Shift+Ctrl+End | End of page and modify selection |

### Instructions

a. First create the folder `~/Library/KeyBindings/` and then the file `DefaultKeyBinding.Dict`

```none
mkdir ~/Library/KeyBindings/
vim DefaultKeyBinding.Dict
```

b. Add the contents below to the new file:

```none
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

_**Note:** The code above assumes you are have substituted the `Command Key` for the `Control Key` in "Keyboard => Modifier Keys..." (see screenshot below). If you haven't, you can try changing the comment between the commented and uncommented blocks._

![](/img/how-to-change-macos-key-bindings/screen2.png)

c. Restart the application you want to use

- - -

Reference:

+ https://gist.github.com/trusktr/1e5e516df4e8032cbc3d

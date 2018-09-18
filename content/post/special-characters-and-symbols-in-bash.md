---
title: "Special Characters and Symbols in Bash"
date: 2018-09-18T15:25:08-04:00
draft: false
author: "Victor Mendonça"
description: "Working with special characters in Bash"
tags: ["Bash", "Linux"]
---


Special Unicode Characters
===

Character Recognition
---

#### Shapecatcher
You can use [Shapecatcher: Draw the Unicode character you want!](http://shapecatcher.com/) to draw a character and try to recognize it.

[&what: Discover Unicode & HTML Character Entities](http://www.amp-what.com/)
[Unicode® character table](https://unicode-table.com/en/)



#### In Bash

[Awesome symbols and characters in a bash prompt - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/25903/awesome-symbols-and-characters-in-a-bash-prompt)

If you can paste the character in Bash, you can dump the character in hex with `hexdump`

```
$ echo "✰" | hexdump -C
00000000  e2 9c b0 0a                                       |....|
00000004

```

Use the hex value to recreate the character:

```
$ echo -e "\xe2\x9c\xb0"
✰
```

List of Special Chars or Symbols
---

✰
 
 

---
title: "Bash: Parameter Expansion (Substitution)"
date: 2017-09-19T01:31:32-04:00
draft: false
author: "Victor Mendonça"
description: "Explanation on manipulating and/or expanding Bash variables"
tags: ["Bash", "Linux"]
---

Default Usage
---

`${PARAMETER}`

Expand parameter value. It can be used for:

* Separating characters from variable names
    - `cd /home/${user}`
* Positional parameter higher than 9
    - `echo "Argument 10 is: ${10}"`

Indirection
---

`${!PARAMETER}`

Expands to the value of the variable named by the value of parameter.

```
var=var1
var1=temp

$ echo ${!var}
temp
```

Case Modification
---

`${PARAMETER^}, ${PARAMETER^^}`
`${PARAMETER,}, ${PARAMETER,,}`
`${PARAMETER~}, ${PARAMETER~~}`

|Operator|Action|
|--|--------------|
|^|Changes first character to upper case|
|^^|Changes all characters to upper case|
|,|Changes first character to lower case|
|,,|Changes all characters to lower case|
|~|Inverts case of first character|
|~~|Inverts case of all characters|


Variable Name (Prefix) Expansion
---

`${!PREFIX*}`
`${!PREFIX@}`

Expands to a list of all set variable names beginning with the string in `PREFIX`.

```
$ echo ${!XDG*}
XDG_CURRENT_DESKTOP XDG_MENU_PREFIX XDG_RUNTIME_DIR XDG_SEAT XDG_SESSION_DESKTOP XDG_SESSION_ID XDG_SESSION_TYPE XDG_VTNR
```

Substring Removal
---

`${PARAMETER#PATTERN}`
`${PARAMETER##PATTERN}`
`${PARAMETER%PATTERN}`
`${PARAMETER%%PATTERN}`

With `#`, it removes the mathing pattern from the beggining of the variable, where `#` removes the shortest match, and `##` removes the longest.

Note the empty spaces in the example below.

```
substr="the quick brown fox jumps over the lazy dog"

$ echo ${substr#* }
quick brown fox jumps over the lazy dog

$ echo ${substr##* }
dog
```

Removing path from a file:

```
mlog=/var/log/clamav/freshclam.log

$ echo ${mlog##*/}
freshclam.log
```

The operator `%` does the same, but at the end of the file.

```
$ echo ${substr% *}
the quick brown fox jumps over the lazy

$ echo ${substr%% *}
the
```

Changing the extension of a file

```
file=123.txt

$ echo ${file%.*}
123

$ echo ${file%.*}.log
123.log
```

Search and Replace
---

`${PARAMETER/PATTERN/STRING}`
`${PARAMETER//PATTERN/STRING}`
`${PARAMETER/PATTERN}`
`${PARAMETER//PATTERN}`

The main diffence is that a single `/` substitutes the first occurrence, while double `//` substitute all occurences:

```
$ echo $substr
the quick brown fox jumps over the lazy dog

$ echo ${substr/the/da}
da quick brown fox jumps over the lazy dog

$ echo ${substr//the/da}
da quick brown fox jumps over da lazy dog

$ echo ${substr/the}
quick brown fox jumps over the lazy dog

$ echo ${substr//the}
quick brown fox jumps over lazy dog
```

### **Anchoring**

You can use `#` and `%` to anchor to the beginning and end respectively

```
var1=00000000

$ echo ${var1/#0/1}
10000000

$ echo ${var1/%0/1}
00000001
```

Offset and Lenght
---

`${PARAMETER:OFFSET}`
`${PARAMETER:OFFSET:LENGTH}`

* Offset removes the amount of characters as specified
* Lenght prints the specified character lenght after offset

***Note:*** *You can also use a negative value for offset and lenght, which will be calculated from the end of the file*

```
$ echo ${substr}
the quick brown fox jumps over the lazy dog
123456789...
```

Removes first 9 chars

```
$ echo ${substr:9}
brown fox jumps over the lazy dog
```

Removes first 3 chars and print the next 5

```
$ echo ${substr:4:5}
quick
```

Use Default
---

`${PARAMETER:-WORD}`
`${PARAMETER-WORD}`

If parameter unset (or null if using `:`), expand to word.

```
$ my_var=gru
$ echo ${my_var:-yyz}
gru

$ unset my_var
$ echo ${my_var:-yyz}
yyz
```

Use Alternate Value
---

`${PARAMETER:+WORD}`
`${PARAMETER+WORD}`

If parameter **is set** (or null if using `:`), expand to word.

```
$ my_var=gru
$ echo ${my_var:+yyz}
yyz

$ unset my_var
$ echo ${my_var:+yyz}

```

Use Default And Assign
---

`${PARAMETER:=WORD}`
`${PARAMETER=WORD}`

If parameter unset (or null if using `:`), expand to word and assign parameter to the value of word.

```
my_var=gru

$ echo ${my_var:=yyz}
gru

$ unset my_var
$ echo ${my_var:=yyz}
yyz

$ echo $my_var
yyz
```

Display Error
---

`${PARAMETER:?WORD}`
`${PARAMETER?WORD}`

If parameter unset (or null if using `:`), display error with word as appendix, otherwise expand parameter.

```
my_var=gru

$ echo ${my_var:?Not set}
gru

$ unset my_var
$ echo ${my_var:?Not set}
bash: my_var: Not set
```

* * *

**Reference:**

* [wiki.bash-hackers.org](http://wiki.bash-hackers.org/syntax/pe)
* [tldp.org](http://www.tldp.org/LDP/abs/html/parameter-substitution.html)
* [gnu.org](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)

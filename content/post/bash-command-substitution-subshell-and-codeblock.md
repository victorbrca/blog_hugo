---
title: "Bash - Command Substitution, Subshell and Codeblock"
date: 2017-01-11T01:38:41-04:00
draft: false
author: "Victor Mendonça"
description: "Quick and simple differences between using ``$(..)``, ``(..)`` and ``{..;}``."
tags: ["Bash", "Linux"]
---

Bash - Command Substitution, Subshell and Codeblock

Quick and simple differences between using `$(..)`, `(..)` and `{..;}`.

* `$(command)` or `` `command` `` - Command substitution (the output of a command replaces the command name) and is executed in a subshell. Please give preference to `$(..)` for better readability
* `( command )` - Command list is executed in a subshell
* `{ command ;}` - Command list is executed in the current shell

**Executing code**

```bash
$ ( echo 123 )
123

$ $(echo 123) # this executes the output of the command
123: command not found

$ { echo 123 ;}
123
```

**Fiding subshell level with built-in `BASH_SUBSHELL` variable**

```bash
$ ( echo $BASH_SUBSHELL )
1
$ ( ( echo $BASH_SUBSHELL ) )
2

$ $($BASH_SUBSHELL)
1: command not found

$ { echo $BASH_SUBSHELL ;}
0
```

**Passing variables to subshell**

```bash
$ var1=1

$ ( echo $var1 )
1

$ $(echo $var1)
1: command not found

$ { echo $var1 ;}
1
```

**Changing and getting variables values in subshell**

```bash
$ var=1

$ ( var=2 ) ; echo $var
1

$ $(var=2) ; echo $var
1

# Because we did not create a subshell,
# variable is available to original shell
$ { var=2 ;} ; echo $var
2
```

### **Other usages**

**Example 1:**

Multiple commands and multiple lines. This can be used with `(..)` and `{..}`.

```bash
$ { echo 1 ; echo 2 ; echo 3; }
1
2
3

$ {
> echo 1
> echo 2
> echo 3;
> }
1
2
3
```

**Example 2:**

Changing the environment for subshell with separate environment. Here, `set -u` is only set in the subshell, so only the first echo will error out.

```bash
unset var1 var2
(
  set -u
  echo $var2
)
echo $var1
```

---
title: "Bash Scripting - Best Practices"
date: 2015-12-18T18:10:57-04:00
draft: false
author: "Victor Mendonça"
description: "Best practices for Bash scripting"
tags: ["Linux", "Bash"]
---

Bash Scripting - Best Practices
===

1 - Readability
---

### 1.1 - Indentation

There are 3 commonly used indentation practices for Bash (I prefer the first method, however all 3 are "accepted"):

* 2 spaces
* 4 spaces
* tabs (usually 8 spaces)  

All examples will be shown using the first indentation method, however for reference here's a comparison between all 3.

**Example:**
```bash
## 2 spaces
if ...
  command
else ...
  command
fi

## 4 spaces
if ...
    command
else ...
    command
fi

## Tabs
if ...
        command
else ...
        command
fi
```

**Indentation for `if` conditional statements**

```bash
if [ test ] ; then
  command
elif [ test ] ; then
  command
else
  command
fi
```

**Indentation for `for` statements**

```bash
for a b c in $A ; do
  command
done
```

**Indentation for `while` and `until` loops**

```bash
while [ true ] ; do
  command
done
```

**Indentation for `case` statements**

```bash
case $VAR in
  true) command ;;
  false)
    command1
    command2
    ;;
  *)
    if [ test ] ; then
      command
    fi
    ;;
esac
```

**Indentation for functions**

```bash
_funct_do_var() {
  commands
}
```
### 1.2 - Comments

#### Code Comments

You should always comment your code. This will make it easier for others to understand, as well as for yourself should you need to change the script a few months (or years) later.

Make sure that the comments make sense. Do not try to save on typing as additional comments can save you (or someone else) a lot of time in the future.

***Example:*** *Commonly used comments*
```bash
startTMATE() {
  ## Starts tmate handling
  # Launch tmate in a detached state
  $TMATE_BIN -S $TMT_SOCKET new-session -d

  # Blocks until the SSH connection is established
  $TMATE_BIN -S $TMT_SOCKET wait tmate-ready

  # Prints the SSH connection string
  $TMATE_BIN -S $TMT_SOCKET display -p '#{tmate_ssh}' > $LOG

  # Prints the read-only SSH connection string
  $TMATE_BIN -S $TMT_SOCKET display -p '#{tmate_ssh_ro}' >> $LOG
}
```

***Example:*** *In-line comments*
```bash
## Sets up package manager aliases based on distro
# Settings for Ubuntu
if [[ "$DISTRO" =~ (Ubuntu|LinuxMint) ]] ; then
  alias aptdate='sudo apt-get update'         # Updates package list
  alias aptgrade='sudo apt-get upgrade'       # Updates all packages
  alias apts='apt-cache search'               # Search for package
  alias aptrm='sudo apt-get remove'           # Removes package

  aptinst() {                                # Install package
    if [ -d "${HOME}/bin/var/log" ] ; then
      LOG=${HOME}/bin/var/log/$(hostname)-package-install.log
    fi

    echo -e "$(date)\t-\tInstalling packages: $*" >> "$LOG"
    sudo apt-get install "$*"
  }
fi
```

#### Block Comment

**Here Document**

You can use a [here document](http://tldp.org/LDP/abs/html/here-docs.html) (`EOF`) to "trick" Bash in creating block comments.

**Example:**

![alt text](http://1.bp.blogspot.com/-X6tmvWFJSy8/VbvmDLJNYNI/AAAAAAAAxoU/nzzVcTGR3ns/s1600/Screenshot%2Bfrom%2B2015-07-31%2B17%253A15%253A23.png)

You can read a bit on my other post [here](http://wazem.blogspot.com/2015/07/how-to-multiline-comments-in-bash.html).

**Bash Built-in Command**

You can also use Bash's bultin `:`, which does nothing.

Here's the definition from Bash's man page:
```bash
: [arguments]
    No effect; the command does nothing beyond expanding arguments and
    performing any specified redirections. A zero exit code is returned.
```

***Example:***
```bash
: '
 your comments here
'
```

### 1.3 - Lines

**Breaking Line of Code**

When breaking long lines of code with `\`, indent the new lines.

***Example:***
```bash
[ -f "/etc/cups/cupsd.conf" ] && echo \
  "The CUPS configuration file exists"
```

**Using Empty Lines**

You can use empty lines to keep your code clean, even inside code blocks.

***Example:***
```bash
# Downloads this weeks photos
if [ "$THIS_WEEKS_PHOTOS_URL" ] ; then
  cd ${UNSPLASH_DIR}
  for PHOTO_URL in $THIS_WEEKS_PHOTOS_URL ; do

    # Checks if jpg is in the URL
    if [[ $(echo $PHOTO_URL | grep -qi jpg ; echo $?) -eq 0 ]] ; then
      PHOTO_FILE_NAME=$(echo $PHOTO_URL | awk -F/ '{print $4}')

    # else, let's add it
    else
      PHOTO_FILE_NAME=$(echo $PHOTO_URL | awk -F/ '{print $4}').jpg
    fi

    # Get the photo and save as file name
    wget --progress=bar $PHOTO_URL -O $PHOTO_FILE_NAME
  done
fi
```

### 1.4 - Misc

* Avoid the use of back-ticks '\`' for command substitution. Use `$(...)` for better readability

2 - Headers and Sections
---

### 2.1 - Script Header

Add descriptive headers to the script outlining it's name, what it does, usage and possibly version.

```bash
#!/bin/bash

################################################################################
################################################################################
# Name:          tmate.sh
# Usage:
# Description:   Runs tmate and outputs remote string to file
# Created:       2014-10-31
# Last Modified:
# Copyright 2014, Victor Mendonca - http://wazem.org
# License: Released under the terms of the GNU GPL license
################################################################################
################################################################################
```

### 2.2 - Sections

Divide your script into sections.

*Put all global script variables in one section*
```bash
#-------------------------------------------------------------------------------
# Sets variables
#-------------------------------------------------------------------------------
```

*Put all functions in one section*
```bash
#-------------------------------------------------------------------------------
# Functions
#-------------------------------------------------------------------------------
```

*Put all major work in one section (where functions get called)*
```sh
#-------------------------------------------------------------------------------
# Starts script
#-------------------------------------------------------------------------------
```

3 - Variables
---

#### 3.1 - Naming

* Give meaningful names to variables
* When using uppercase, make sure the variable is not already being used
* Avoid starting variables with `_`

#### 3.2 - Calling

Double quote variables to prevent globbing or word splitting.

4 - Functions
---

Try to define your functions at the beginning of the script (see sections).

#### 4.1 - Naming

* Should start with lower case
* It's good to start with `_`
* Upper and lower case are also good

***Example:***
```bash
_lowerIt () {
  BASH_MAIN_VERSION=$(echo $BASH_VERSION | awk -F. '{print $1}')
  if [[ $BASH_MAIN_VERSION -lt 4 ]] ; then
    echo "Not supported with your version of bash"
    return
  fi

  if [ "$1" ] ; then
    echo ${1,,}
  fi
}
```

### 4.2 - Local Variables

Keep your function variables local if they are not being used outside of the function.

***Example:*** This script will output "$2400" only once
```bash
_myFunction() {
  local extract
  extract='$2400'
  echo "$extract"
}

_myFunction
echo "$extract"
```

5- Code Smart
---

**New and Deprecated Features**

Get to know your version of Bash and any features it may have.

***Example:*** Old and new arithmetic expansion
```bash
# New way for arithmetic expansion
$ echo $((4*25/2))
50

# Old way for arithmetic expansion, which will be deprecated
$ echo $[4*25/2]
50
```

**New features**

Bash 4 packs new features (like parameter expansion and output redirects). Get familiar with them as they can save you a lot of time.

***Examples:*** Parameter expansion
```bash
$ title="bash best practices"

## Case substitution
$ echo ${title^^}
BASH BEST PRACTICES

## String substitution
$ echo ${title/best/worst}
bash worst practices
```

***Examples:*** stdout and stderr output redirect
```bash
# New
ls 123 &> /dev/null

# Old
ls 123 > /dev/null 2>&1
```

6 - Software
---

You can also use software to help with your scripts.

#### 6.1 - Syntax Highlighting

Most of today's editors include syntax highlighting, even VIM. Syntax highlighting will help you catch mistakes like a unclosed bracket or quote.

### 6.2 - Lint

Some editors, like [Sublime Text](https://www.sublimetext.com/) include plugins for [lint software](https://en.wikipedia.org/wiki/Lint_(software)). They can be of great help for you coding.

* [SublimeLinter 3](http://www.sublimelinter.com/en/latest/)

### 6.3 - Online Tools

There are also sites that can help checking your script (similar to lint), or a small set of commands.

* [explainshell.com](http://explainshell.com/) - match command-line arguments to their help text
* [shellcheck.net](http://www.shellcheck.net/) - automatically detects problems in sh/bash scripts and commands

#### **References**
* [TFDP - Unofficial Shell Scripting Stylesheet](http://tldp.org/LDP/abs/html/unofficialst.html)
* [Bash Hackers Wiki - Scripting with style](http://wiki.bash-hackers.org/scripting/style)
* [Greg's Wiki - Bash Practices](http://mywiki.wooledge.org/BashGuide/Practices)

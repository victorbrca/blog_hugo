---
title: "Count Lines of Codes With Cloc"
date: 2019-01-15T10:52:48-05:00
draft: false
author: "Victor Mendonça"
description: "How to count lines of code in a folder/project"
tags: ["Linux", "Programming"]
---

Have you ever had the nead to count the lines of code in a project or a folder? If you did, or want to, `cloc` is a neat utility that can help with just that.

As per it's description on the [GitHub](https://github.com/AlDanial/cloc) page, "_cloc counts blank lines, comment lines, and physical lines of source code in many programming languages_". It displays a summary of file types and counted files, and then it breaks down a list of lines of code per language.

Here's the output from my [bash-config](https://github.com/victorbrca/bash-config) project:

```
 ~/Git/bash-config $ cloc .
      45 text files.
      43 unique files.                              
       4 files ignored.

github.com/AlDanial/cloc v 1.80  T=0.29 s (146.6 files/s, 10151.3 lines/s)
--------------------------------------------------------------------------------
Language                      files          blank        comment           code
--------------------------------------------------------------------------------
Bourne Again Shell               38            355            440           1525
Bourne Shell                      2             41             32            276
Markdown                          2             52              0            187
--------------------------------------------------------------------------------
SUM:                             42            448            472           1988
--------------------------------------------------------------------------------
```

And here's a view of a more complex project with multiple languages:

```
     346 text files.
     338 unique files.                                          
     110 files ignored.

github.com/AlDanial/cloc v 1.80  T=0.75 s (319.4 files/s, 45153.6 lines/s)
----------------------------------------------------------------------------------------
Language                              files          blank        comment           code
----------------------------------------------------------------------------------------
Bourne Shell                            102           3417           3753          14810
Bourne Again Shell                       28           1209           1040           5480
XML                                       3             88             13            713
DOS Batch                                 6             90             24            448
Markdown                                  5            122              0            411
Visual Basic                              5             56             31            331
ERB                                      66             69              0            322
HTML                                      4             12             62            182
SQL                                      10             65            203            166
Korn Shell                                2             33             65            115
Velocity Template Language                1              0              0            114
Python                                    3             18             45             59
CSS                                       1              1              1             24
Java                                      1              3              3             22
Ruby                                      1              6              5             11
----------------------------------------------------------------------------------------
SUM:                                    238           5189           5245          23208
----------------------------------------------------------------------------------------
```

`cloc` is available on many distros default repo, as well as npm install. To install it use:

    sudo apt install cloc                  # Debian, Ubuntu
    sudo yum install cloc                  # Red Hat, Fedora
    sudo dnf install cloc                  # Fedora 22 or later
    sudo pacman -S cloc                    # Arch
    sudo emerge -av dev-util/cloc          # Gentoo https://packages.gentoo.org/packages/dev-util/cloc
    sudo apk add cloc                      # Alpine Linux
    sudo pkg install cloc                  # FreeBSD
    sudo port install cloc                 # Mac OS X with MacPorts
    brew install cloc                      # Mac OS X with Homebrew
    choco install cloc                     # Windows with Chocolatey
    scoop install cloc                     # Windows with Scoop

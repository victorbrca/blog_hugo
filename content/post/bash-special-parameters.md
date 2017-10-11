---
title: "Bash Special Parameters"
date: 2017-09-26T22:46:27-04:00
draft: false
author: "Victor Mendonça"
description: "Quick definition of Bash's special parameters"
tags: ["Bash", "Linux"]
---

Special parameters are set by the shell to store information about aspects of its current state, such as the number of arguments and the exit code of the last command. Special parameters can only be referenced and cannot have it's value assigned.

Special parameters are: `$*, $@, $#, $$, $!, $?, $0, $-, $_`

Parameter |Definition
----------|--------------
$* |List of arguments (as a string)
$@ |List of arguments (as an array)
$# |Number of positional parameters
$$ |PID of the current shell
$! |PID of the last command executed in the background
$? |Exit code of the last-executed command
$0 |Path to the currently running script
$- |Current shell option flags
$_ |Gives the last argument to the previous command

---
title: "Linux find Command"
date: 2022-11-18T08:42:34-05:00
draft: false
author: "Victor Mendonça"
description: "A quick overview of the Linux find command"
tags: ["Linux", "bash"]
---

The Linux `find` command is a very versatile tool in the pocket of any Linux administrator. It allows you to quickly search for files and take action based on the results.

The basic construct of the command is:

```
find [options] [path] [expression]
```

The `options` part of the command controls some basic functionality for `find` and I'm not going to cover it here. I will instead quickly look at the components for the expression part of the command, and provide some useful examples.

Expressions are composed of tests, actions, global options, positional options and operators:

- **Test** - returns true or false (e.g.: `-mtime`, `-name`, `-size`)
- **Actions** - Acts on something (e.g.: `-exec`, `-delete`)
- **Global options** - Affect the operations of tests and actions (e.g.:`-depth`, `-maxdepth`,)
- **Positional options** - Modifies tests and actions (e.g.: `-follow`, `-regextype`)
- **Operators** - Include, join and modify items (e.g.: `-a`, `-o`)

## Operators

Operators are the logical `OR` and `AND` of find, as well as negation and expression grouping.

| Operator       |  Description                                                                                                                    |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `\( expr \)`   | Force precedence                                                                                                               |
| `!` or `-not`  | Negate                                                                                                                          |
| `-a` or `-and` | Logical `AND`. If two expressions are given without `-a` find will take it as implied. expr2 is not evaluated if expr1 is false |
| `-o` of `-or`  | Logical `OR`. expr2 is not evaluated if expr1 is true                                                                           |
| `,`            | Both  expr1  and expr2 are always evaluated.  The value of expr1 is discarded                                                                                                                               |

You can use the operators for repeated search options with different values.

_**Example 1:** Find files with multiple file extensions_

```bash
find ${jboss_dplymnt_dir} -type f -size -2k \( \
  -name '*.deployed' -o -name '*.dodeploy' \
  -o -name '*.skipdeploy' -o -name '*.isdeploying' \
  -o -name '*.failed' -o -name '*.isundeploying' \
  -o -name '*.pending' -o -name '*.undeployed' \
\)
```

_**Example 2:** find MOV or MP4 files that do not have 'h265' in the file name_

```bash
find . \( \
  ! -name '*h265*' -a \( \
    -name '*.mp4' -o -name '*.MP4' \
    -o -name '*.mov' -o -name '*.MOV' \
  \) \
\)
```

Or to negate search options.

_**Example 3:** find files that don't finish with the '.log' extension_

```bash
find . -type f -not -name '*.log'
```

_**Example 4:** Excludes everything in the folder named 'directory'_

```bash
find -name "*.js" -not -path "./directory/*"
```

## Global Options

Two very useful global options are `-maxdepth` and `-mindepth`.

+ **maxdepth levels**: Descend at most levels (a non-negative integer) levels of directories below the starting-points. `-maxdepth 0` means only apply the tests and actions to the starting-points themselves.
+ **mindepth levels**: Do not apply any tests or actions at levels less than levels (a non-negative integer). `-mindepth 1` means process all files except the starting-points.

_**Example 5:** find all files with '.txt' extension on the current dir and **do not descend into subdirectories**_

```bash
find . -maxdepth 1 -name '*.txt'
```

## Tests

This is where you can target specific properties about the files that you are searching for. Some of my preferred tests are:

- `-iname` - Like `-name`, but the match is case insensitive
- `-size` - Matches based on size (e.g.: `-size -2k`, `-size +1G`)
- `-user` - File Belongs to username
- `-newer` - File is newer than file. It can be a powerful option


_**Example 6:** Find files for user_

```bash
find . -type f -user linustorvalds
```

_**Example 7:** Find files larger than 1GB_

```bash
find . -type f -size +1G
```

_**Example 8:** Here's a hidden gem! Let's say you need to find what files a program is creating. All you need to do is create a file, run the program, and then use `-newer` to find what files were created_

```bash
# Create a file
touch file_marker

# Here I run the script or program
./my_script.sh

# Now I can use the file I created to find newer files that were created by the script
find . -type f -newer 'file_marker'
```

## Actions

Actions will execute the specified action against the matched filenames. Some useful actions are:

- `-ls` - Lists matched files with `ls -dils`
- `-delete` - Deletes the matched files
- `-exec` - Execute the specified command against the files
- `-ok` - Prompts user for before executing command
- `-print0` - Prints the full name of the matched files followed by a null character

## Real-life Scenarios

#### Get a confirmation prompt

Use `-ok` to get a confirmation prompt before executing the action on the matched files.

```bash
find . -type f -name '*.txt' -ok rm -rf {} \;
< rm ... ./file_10.txt > ? y
< rm ... ./file_9.txt > ? y
< rm ... ./file_8.txt > ?
```

#### Deleting files

Whenever possible, use `-delete` instead of `-exec rm -rf {} \;`.

```bash
find . -type f -name "*.bak" -exec rm -f {} \;
```

Instead use:

```bash
find . -type f -name "*.bak" -delete
```

#### Using `xargs` and `{} +`

The command bellow will run into a problem if files or directories with embedded spaces in their names are encountered. `xargs` will treat each part of that name as a separate argument:

```bash
find /usr/include -name "*.h" -type f | xargs grep "#define UINT"
```

There are two ways to avoid that. You could tell both `find` and `xargs` to use a `NUL` character as a separator:

```bash
find /usr/include -name "*.h" -type f -print0 | xargs -0 grep "#define UINT"
```

Or, you could avoid use of `xargs` altogether and let `find` invoke `grep` directly:

```bash
find /usr/include -name "*.h" -type f -exec grep "#define UINT" {} +
```

That final `+` tells `find` that `grep` will accept multiple file name arguments. Like `xargs`, `find` will put as many names as possible into each invocation of `grep`.

#### Find and Move

Find files and move them to a different location.

+ `-v` - For verbose
+ `-t` - Move all `SOURCE` arguments into `DIRECTORY`
+ `-exec command {} +` - As we saw before, this variant of the `-exec` action runs the specified command on the selected files, but the command line is built by appending each selected file name at the end; the total number of invocations of the command will be much less than the number of matched files

No need for `\;` at the end.

```bash
find . -maxdepth 1 -name 'sa-*' -exec mv -v -t rsa-todelete/ {} +
```

#### Search PDF content

Here we search the contents of PDF files for specif text. Two options are shown below, each require the install of an additional package (`pdfgrep` and `ripgrep-all`).

With `pdfgrep`:

```bash
find . -iname '*.pdf' -exec pdfgrep [pattern] {} +
```

With `ripgrep-all`:

```bash
find . -iname '*.pdf' | xargs rga -H PURGE_TRAN_LOG.sh
```

#### Check if files exists

One of the problems with `find` is that it doesn't return `true` or `false` based on search results. But we can still use it on our `if` condition by greping the result.

```bash
if find /var/log -name '*.log' | grep -q log ; then
  echo "File exists"
fi
```

### Conclusion

You should now have a better understanding of the `find` command, as well as some nifty use cases to impress you co-workers.

If you found something useful or want to share an interesting use for `find`, please leave a comment below.

---
title: "Quick Guide to SUID, SGID and Sticky Bit"
date: 2020-09-11T16:00:00-04:00
draft: false
author: "Victor Mendonça"
description: "Quick Guide to using SUID, SGID and Sticky Bit"
tags: ["Linux"]
---

This is a quick guide on how to configure and use SGID, SUID and the sticky bit on Linux. I will not get into a lot of details, but I will add comments and notes that might help you understand or overcome a few common issues.

### SUID - Set-user Identification

When a command or script with SUID bit set is run, its effective UID becomes that of the owner of the file, rather than of the user who is running it.

```none
-rws-----
```

**Note**: _SUID does not work on scripts that start with a shebang (`#!`)_

```none
# chmod u+s [file]
-rwsr--r--. 1 root root 0 Mar 16 21:48 test

# chmod 4744 [file]
-rwsr--r--. 1 root root 0 Mar 16 21:48 test
```

**Note**: _A capital 'S' (-rw**S**r--r--) indicates that the execute bit is not set_

### SGID - Set-group identification

SGID permission is similar to the SUID permission. The main difference is that when a script or command with SGID set is run, it runs as if it were a member of the same group in which the file is a member.

```none
-rwxr-sr--
```

**Setting SGID**

```none
# chmod g+s [file]
-rwxr-sr--. 1 root root 0 Mar 16 21:48 test

# chmod 2754 [file]
-rwxr-sr--. 1 root root 0 Mar 16 21:48 test
```

**Note**: _A capital 'S' (-rwxr-**S**r--) indicates that the execute bit is not set_

### Sticky bit  

Anyone can write, but only the owner can delete the files (just like `/tmp`).

```none
drwxrwxrwt
```

Sticky bit is usually set on directories. Setting the sticky bit on a folder does nothing (on Linux).

**Setting sticky bit**

```none
# chmod o+t [dir]
-rwxr-r-t. 1 root root 0 Mar 16 21:48 test

# chmod 1755 [dir]
-rwxr-xr-t. 1 root root 0 Mar 16 21:48 test
```

**Notes**:

+ _A capital 'T' indicates that the execute bit is not set_
+ *You should give write permission to make sure that the target users can write to the folder*

### Additional Special Permissions

A `.` can represent special permissions (SELinux related).

```none
-rw-rw-rw-.  
```

A `+` indicates ACLs are applied.

```none
-rw-rw-rw-+
```

### Cheat Table

| Mode | Octal | Symbolic|
|:--|:--|:--|
| SUID |4755 |u+s |
| SGID |2775 |g+s |
| Sticky Bit |1777 |o+t |

**Note**: _Octal mode is not an absolute translation to symbolic mode as symbolic changes only the specified permission set (user, group **OR** others), while octal overwrites all permission sets (user, group **AND** others)_

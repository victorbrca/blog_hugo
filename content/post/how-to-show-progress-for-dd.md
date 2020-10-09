---
title: "How to Show Progress for dd"
date: 2020-10-09T11:28:28-04:00
draft: false
author: "Victor Mendonça"
description: "Two different ways to show progress for dd"
tags: ["Bash", "Linux", "Hardware"]
---

If you have reached this page I expect that you already know what `dd` is. But you if you don't, `dd` is a command-line utility that is used to convert and copy files. It's commonly found on various (Linux) distro help pages that show new users how to setup USB (or Micro SD) drives to install or run (like Raspberry Pi) the OS.

One of the main problems is that when running the `dd` utility, it does not provide any information on the current status of the copy process. This can make users anxious not knowing if it's done or not.

## The Solutions

#### 1. Running `dd` with the `status` option

This requires you to be running `dd` version 8.24 and above. If you are running a modern Linux distro you are most likely covered.

You can check your version with `dd --version`:

```none
➤ dd --version
dd (coreutils) 8.32
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Written by Paul Rubin, David MacKenzie, and Stuart Kemp.
```

We will also need to use `oflag=sync` to make sure that we are really syncing as data is being copied so we get a better sense of when it's done. Otherwise `dd` goes blank at the end as it's trying to sync.

_**Note:** while using `oflag=sync` makes the copy slower, using a higher block size will help speed up the process (`bs=4M` instead of `bs=1M`)_

##### **The command:**

_**Structure:**_
```none
dd bs=4M if=/path/to/input of=/path/to/output status=progress oflag=sync
```

_**Example:** here are backing up the contents of `/dev/sdf` to a file (ubuntu-server-20.04.1-updated.iso)_
```none
sudo dd bs=4M if=/dev/sdf of=ubuntu-server-20.04.1-updated.iso status=progress oflag=sync
```

_**The output:**_
![](img/how-to-show-progress-for-dd/dd-2.png)

#### 1. Running `dd` with `pv`

In reality this should be option 1 as it gives a nicer output with a progress bar, ETA and other data. `pv` is a terminal-based tool for monitoring the progress of data through a pipeline. It can potentially be used with any pipe.

##### **The command:**

_**Structure:**_
```none
pv -tpreb /path/to/input | sudo dd of=/path/to/output bs=4M oflag=sync
```

Options used:

+ `-t` - “Turn the timer on”
+ `-p` - “Turn the progress bar on”
+ `-r` - “Turn the rate counter on”
+ `-e` - “Turn the ETA timer on”
+ `-b` - “Turn the total byte counter on”


_**Example:** Here we are copying the image file to `/dev/sdf`_
```none
pv -tpreb ubuntu-20.04.1-preinstalled-server-arm64+raspi.img | sudo dd of=/dev/sdf bs=4M oflag=sync
```

_**The output:**_
![](img/how-to-show-progress-for-dd/pv-with-sync.png)

- - -

#### Putting all in a Bash alias for re-use

Add the function below to your `~/.bash_aliases` and it will be available whenever you need. Call it with `dd_iso [image] [device]` and it will use `pv` if it's already installed in your syste.

```bash
dd_iso ()
{
    usage="usage: dd_iso [image] [device]";
    if [[ $# -lt 2 ]]; then
        echo "$usage";
        return 0;
    else
        if [[ $# -eq 2 ]]; then
            iso="$1";
            device="$2";
        fi;
    fi;
    if [[ ${iso##*.} != iso && ${iso##*.} != img ]]; then
        echo "The first parameter should be an iso";
        return 1;
    else
        if [[ ! -b "$device" ]]; then
            echo "The second parameter needs to be a device";
            return 1;
        fi;
    fi;
    device_type="$(basename "$(readlink -f "/sys/class/block/${device##*/}/..")")";
    if [[ "$device_type" != "block" ]]; then
        echo "Do not specify a parition as the device";
        return 1;
    fi;
    sudo dd bs=4M if="$iso" of="$device" status=progress oflag=sync
}
```

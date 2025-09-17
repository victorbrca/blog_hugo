---
title: "How to install Windows on LXD"
date: 2025-09-17T16:05:11-04:00
draft: false
author: "Victor Mendonça"
description: "Because sometimes even Linux folks need Windows"
tags: ["Linux", "Windows", "Virtualization"]
---

![](/img/how-to-install-windows-on-lxd/0-windows-on-linux.png)

Yes, you can run Windows 10 or 11 inside LXD. It’s actually not that hard—just a few prep steps and some command-line magic. These instructions are written for Arch, but the process is nearly the same on other distros (package names may change a bit).

Let’s go.

### Step 1: Preparing the Image

a. Grab the ISO:

  Download Windows from Microsoft like a law-abiding citizen: 👉 https://www.microsoft.com/en-ca/software-download

b. Install the required packages

```none
pacman -S distrobuilder libguestfs wimlib cdrkit
```

c. Repack the ISO for LXD. Note that I'm creating a Windows 10 VM

```none
sudo distrobuilder repack-windows --windows-arch=amd64 Win10_22H2_English_x64v1.iso Win10_22H2_English_x64v1-lxd.iso
```

### Step 2: Prepping the VM

a. Create an empty VM:

```none
$ lxc init windows10 --empty --vm
Creating windows10
```

b. Give it a bigger disk

```none
$ lxc config device override windows10 root size=55GiB
Device root overridden for windows10
```

c. Add CPU and memory

```none
lxc config set windows10 limits.cpu=4 limits.memory=6GiB
```

d. Add a trusted platform module device

```none
$ lxc config device add windows10 vtpm tpm path=/dev/tpm0
Device vtpm added to windows10
```

e. Add audio device

```none
lxc config set windows10 raw.qemu -- "-device intel-hda -device hda-duplex -audio spice"
```

f. Add the install disk. Note that it needs the absolute path

```none
$ lxc config device add windows10 install disk source=/home/victor/Downloads/OS/Win10_22H2_English_x64v1-lxd.iso boot.priority=10
Device install added to windows10
```

### Step 3: Install Windows

a. Start the VM and connect:

```none
lxc start windows10 --console=vga
```

b. "Press any key" to boot from CD and install Windows

c. Go through the usual Windows install:

![](/img/how-to-install-windows-on-lxd/1-windows-install.png)

You can also use the LXD WebUI console to monitor reboots:

![](/img/how-to-install-windows-on-lxd/2-windows-finishing-install.png)

### Step 3: Clean Up

Remove the install ISO when you’re done:

```none
$ lxc config device remove windows10 install
Device install removed from windows10
```

### Conclusion

And that’s it—you now have Windows running on LXD. Pretty painless, right?

![](/img/how-to-install-windows-on-lxd/4-windows-installed.png)

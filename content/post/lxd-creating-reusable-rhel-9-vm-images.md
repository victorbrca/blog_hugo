---
title: "LXD: Creating Reusable RHEL 9 VM Images"
date: 2025-09-25T05:00:47-04:00
draft: false
author: "Victor Mendonça"
description: "Save time by creating a reusable RHEL 9 VM image with LXD."
tags: ["Linux", "Containers", "Virtualization"]
---

Following the LXD theme of my previous posts, today I’m going to walk you through creating a RHEL 9 VM on LXD, and then show you how to turn that VM into an image. This way, you can spin up new RHEL 9 VMs whenever you want—without having to sit through the whole install/setup process again.

To follow along, you’ll need an active Red Hat subscription. If you’re doing this for personal use (like self-development, home lab tinkering, or just because you enjoy virtual machines as much as Netflix), you can [download RHEL for free](https://developers.redhat.com/products/rhel/download) when you sign up for the **Red Hat Developer Subscription for Individuals.**


> [No-cost Red Hat Enterprise Linux Individual Developer Subscription: FAQs](https://developers.redhat.com/articles/faqs-no-cost-red-hat-enterprise-linux)
>
> **1. What is the Red Hat Developer program's Red Hat Developer Subscription for Individuals?**
>
> The Red Hat Developer Subscription for Individuals is a no-cost offering of the Red Hat Developer program and includes access to Red Hat Enterprise Linux among other Red Hat products. It is a program and an offering designed for individual developers, available through the Red Hat Developer program.
>
> **2. What Red Hat Enterprise Linux developer subscription is made available at no cost?**
>
> The no-cost Red Hat Developer Subscription for Individuals is available and includes Red Hat Enterprise Linux along with numerous other Red Hat technologies. Users can access this no-cost subscription by joining the Red Hat Developer program at developers.redhat.com/register. Joining the program is free.

### Creating the VM

I’ll be using RHEL 9 here, but the same steps apply to other versions.

a. Create an empty VM:

```none
lxc init rhel9 --vm --empty
```

b. Give the VM a 20GB root disk:

```none
lxc config device override rhel9 root size=20GiB
```

_Optional: increase CPU and RAM limits if needed (depending on your profile)._

c. Add the install disk:

```none
lxc config device add rhel9 install disk source=/home/victor/Downloads/OS/rhel-9.6-x86_64-dvd.iso boot.priority=10
```

d. Start the VM and go through the RHEL install (not covered here):

```none
lxc start rhel9
```

e. Remove the install disk when the install is complete:

```none
lxc device remove rhel9 install
```

### Install the LXD agent

It’s a good idea to include the LXD agent in your image—otherwise, you’ll miss out on nice integration features.

a. Mount the config device:

```none
mkdir /mnt/iso

mount -t virtiofs config /mnt/iso

cd /mnt/iso
```

b. Install and reboot:

![picture 0](/img/lxd-creating-reusable-rhel-9-vm-images/image-1.png)

c. Check that the agent is running:

```none
$ lxc exec rhel9 -- systemctl is-active lxd-agent
active
```

### Prepare the OS

#### Run Updates

This section is optional and you only need to follow in case you want to get your image updated with the current packages.

a. Connect the VM to your Red Hat account:

_I'm using Red Hat Enterprise Linux Individual Developer Subscription_

```none
# rhc connect
Connecting rhel9 to Red Hat.
This might take a few seconds.

Username: jdoe
Password: *********

● Connected to Red Hat Subscription Management
● Connected to Red Hat Insights
● Activated the Remote Host Configuration daemon

Successfully connected to Red Hat!

Manage your connected systems: https://red.ht/connector
```

b. Install updates and reboot:

```none
# yum update -y

# dnf needs-restarting -r || reboot
```

c. Install any packages or make tweaks you’d like baked into the image.

#### Clean up the OS

Before creating the image, let’s tidy things up.

a. Disconnect the VM from Red Hat's network:

```none
# rhc disconnect
Disconnecting rhel9 from Red Hat.
This might take a few seconds.

● Deactivated the Remote Host Configuration daemon
● Disconnected from Red Hat Insights
● Disconnected from Red Hat Subscription Management

Manage your connected systems: https://red.ht/connector
```

b. Connect to the terminal as root (either via the Terminal tab on LXD's UI, or with `lxc exec rhel9 -- /bin/bash`).

c. Clean DNF cache:

```none
dnf clean all
```

d. Clean subscription-manager data:

```none
subscription-manager clean
```

e. Remove SSH keys:

```none
rm -f /etc/ssh/ssh_host_*
```

f. Clean up logs:

```none
journalctl --vacuum-time=1d

rm -rf /var/log/* /tmp/*
```

g. Change hostname to a default one:

```none
hostnamectl set-hostname localhost.localdomain
```

h. Delete the machine ID:

```none
rm -f /etc/machine-id

rm -f /var/lib/dbus/machine-id

touch /etc/machine-id
```

i. Delete the user if you created one (optional):

```none
userdel -rf redhat
```

j. Delete root's history (optional):

```none
rm -f /root/.bash_history
```

k. Shutdown the machine:

```none
poweroff
```

#### Edit the VM Metadata

Before publishing, edit metadata so your image looks nice and tidy:

```none
lxc config metadata edit rhel9
```

Example:

```yaml
architecture: x86_64
creation_date: 1758825660
expiry_date: 0
properties:
  architecture: x86_64
  description: Red Hat Enterprise Linux 9.6 (Plow)
  os: rhel
  release: Plow
  version: "9.6"
templates: {}
```

_Tip: to get the current date in Unix time, use `date +%s`_

### Create the Image

We are now ready to create the image with `lxc publish`:

```none
$ lxc publish rhel9 --alias rhel96.x86_64
Instance published with fingerprint: e4ff15c38889676cb3fe1ae0268122294856414b40275ef61cfe0baf23330ae3
```

### Launch a New VM from the Image

And finally, create a fresh VM without all the hassle:

```none
$ lxc launch rhel96.x86_64 rhel9-test
Launching rhel9-test
```

That’s it—you’ve got a reusable RHEL 9 image. Future VM creation is now basically a one-liner. Way better than babysitting an ISO every time.

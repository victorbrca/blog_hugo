---
title: "How to Quickly Create an Encrypted USB Drive on KDE Plasma"
date: 2020-10-21T15:32:17-00:00
draft: false
author: "Victor Mendonça"
description: "How to quickly create an encrypted USB drive on KDE Plasma"
tags: ["Linux", "Hardware", "Encryption"]
---

Need to create an encrypted USB drive? Are your running KDE Plasma?

Then great! Here are 6 simple steps on how to quickly and painlessly create an encrypted USB drive with [KDE Partition Manager](https://apps.kde.org/en/partitionmanager).

### Let's start

a. Start by inserting your USB drive

b. Now launch `KDE Parition Manager` and type your password

![](/img/how-to-create-an-encrypted-usb-drive-on-kde-plasma/admin-prompt.png)

c. Select the USB drive and right click to delete the existing partition

> **⚠️ WARNING:** remember, this will delete all your existing files)

![](/img/how-to-create-an-encrypted-usb-drive-on-kde-plasma/delete.png)

d. Right click to create a new partition

![](/img/how-to-create-an-encrypted-usb-drive-on-kde-plasma/new.png)

e. Select your "File system" type, check "Encrypt with LUKS", set the "Password" and "Label" and click on "Ok"

![](/img/how-to-create-an-encrypted-usb-drive-on-kde-plasma/create.png)

f. Click on apply and you are done. Go grab a coffee because you worked really hard you deserve it

#### Mounting the Drive

The mounting process should be as painless as setting up the USB drive.

a. Insert the USB drive and click on "Mount" under "Disk & Device" on your system tray

![](/img/how-to-create-an-encrypted-usb-drive-on-kde-plasma/mounting.png)

b. Type in your very secure password (mine is "password123") and profit

_**Tip:** You can set it to remember if you really trust your own machine_

![](/img/how-to-create-an-encrypted-usb-drive-on-kde-plasma/password.png)

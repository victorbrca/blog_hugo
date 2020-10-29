---
title: "How to Fix Sddm on Multiple Screens"
date: 2018-06-29T19:20:45-04:00
draft: false
author: "Victor Mendonça"
description: "How to Fix Sddm on Multiple Screens"
tags: ["Linux", "KDE"]
---

When running multiple screens on KDE with SDDM, your configuration will not be loaded until you actually login to the KDE desktop, so you might end up having screens out of order, or enabled/disabled when they should not be. To fix this is simple, and here's how.

Let's get a list of the devices name, size and position with `xrandr`:

```
$ xrandr | grep ' connected'

DP-1 connected (normal left inverted right x axis y axis)
DP-3 connected 1920x1080+1920+0 (normal left inverted right x axis y axis) 480mm x 270mm
DP-5 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 598mm x 337mm
```

As you can see, on my computer I have 3x monitors:

* DP-1 - Connected on the first port but disabled
* DP-3 - My secondary monitor; Resolution 1920x1080; position 1920x0
* DP-5 - My main monitor; Resolution 1920x1080; position 0x0

So we will start by adding the right configuration to `/usr/share/sddm/scripts/Xsetup`

```
$ sudo vim /usr/share/sddm/scripts/Xsetup

#!/bin/sh
# Xsetup - run as root before the login dialog appears

xrandr --output DP-1 --off
xrandr --output DP-5 --mode 1920x1080 --pos 0x0 --rotate normal --output DP-3 --mode 1920x1080 --pos 1920x0 --rotate normal
```

**Explanation**:

* The first line disables the monitor connected to the first port `xrandr --output DP-1 --off`
* The second line sets the right dimension and position for my other two monitors

Now we need to add the config to SDDM:

```
$ sudo vim /etc/sddm.conf

[XDisplay]
# Xsetup script path
# A script to execute when starting the display server
DisplayCommand=/usr/share/sddm/scripts/Xsetup
```

And now a simple reboot will take care of the changes:

```
sudo reboot
```

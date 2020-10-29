---
title: "Gnome Touchpad Settings Missing in Arch Xps 13"
date: 2017-09-23T22:47:40-04:00
draft: false
author: "Victor Mendonça"
description: "Solution for fixing an issue were the Gnome touchpad settings disappear."
tags: ["Linux", "Arch", "Hardware"]
---

I had an issue where the Gnome extension '[Touchpad Indicator](https://extensions.gnome.org/extension/131/touchpad-indicator/)' stopped working on my xps 13 (Arch). After looking a bit further, it seems that the Gnome Touchpad settings had also stopped working. All I could see was the mouse settings, and the touchpad section was completelly gone.

* * *

**Solution:**

With Gnome 3.20, `xf86-input-synaptics` is not longer supported, and you should use `xf86-input-libinput` instead.

You can check what is installed on your Arch system with `pacman -Q | grep input`. In my case, I had both packages installed:

```
$ pacman -Q | grep input
inputproto 2.3.2-1
libinput 1.8.2-1
xf86-input-libinput 0.26.0-1
xf86-input-synaptics 1.9.0-1
xorg-xinput 1.6.2-1
```

Remove `xf86-input-synaptics` and any configuration file (like `/etc/X11/xorg.conf.d/50-synaptics.conf`), install `xf86-input-libinput` and reboot. That should get your configuration working again.

---
title: "Touchpad Gestures on XPS 13"
date: 2019-04-15T18:14:35-04:00
draft: false
author: "Victor Mendonça"
description: "Instructions on how to setup touchpad gestures on Dell's XPS 13 (9360)"
tags: ["Hardware", "Linux"]
---

The XPS 13 is a great laptop and it comes with an equally matched touchpad. Setting up touch gestures is pretty easy, and we will cover what needs to be done to get it working on Arch.

First, let's install all the needed software:


+ `extra/libinput`
+ `aur/libinput-gestures`

After installing the 2 packages you should be able to run `libinput-gestures -d` as root and try the pre-defined gestures. You should see an output when you swipe left/right/up/down with 3/4 fingers and pinch in/out.

```none
# libinput-gestures -d

libinput-gestures: session unknown+unknown on Linux-5.0.6-arch1-1-ARCH-x86_64-with-arch, python 3.7.3, libinput 1.13.0
/usr/bin/libinput-gestures: hash 4cc3250c5befc6926c04b3e499114677
Gestures configured in /etc/libinput-gestures.conf:
swipe up           _internal ws_up
swipe down         _internal ws_down
swipe left         xdotool key alt+Right
swipe right        xdotool key alt+Left
pinch in           xdotool key super+s
pinch out          xdotool key super+s
libinput-gestures: device /dev/input/event10: DLL075B:01 06CB:76AF Touchpad
libinput-gestures: SWIPE up 3 [-33.88000000000001, -604.8000000000001]
   _internal ws_up
libinput-gestures: SWIPE right 3 [755.69, -17.479999999999993]
   xdotool key alt+Left
```

Let's add your user to the `input` group (because it should not run as `root`), then re-login:

```none
sudo gpasswd -a [user] input
```

Now let's edit our personal config file in `~/.config/libinput-gestures.conf`. Using `xdotool` create a map of what keyboard shortcuts you would like to map your gestures to. Keep in mind you can also map it to commands.

For example, my config file looks like this:

```none
# Gestures
gesture swipe up 3 xdotool key Ctrl+F9
gesture swipe up 4 xdotool key Ctrl+F10
gesture swipe left 3 xdotool key Ctrl+Alt+Right
gesture swipe right 3 xdotool key Ctrl+Alt+Left
gesture swipe down 3 xdotool key Super+Down
```

And the keyboard shortcuts are mapped to (KDE):

- `Ctrl+F9` - Show all windows (current desktop)
- `Ctrl+F10` - Show all windows (all desktops)
- `Ctrl+Alt+Right` - Move to desktop on right
- `Ctrl+Alt+Left` - Move to desktop on left
- `Super+Down` - Desktop preview

Let's configure the gestures to start with the desktop environment:

```none
$ libinput-gestures-setup autostart
```

And start the gestures for the current session:

```none
$ libinput-gestures-setup start

Icon theme "papirus" not found.
Icon theme "ubuntu-mono-dark" not found.
Icon theme "Mint-X" not found.
Icon theme "elementary" not found.
Icon theme "gnome" not found.
libinput-gestures started.
```

Enjoy your new setup gestures.

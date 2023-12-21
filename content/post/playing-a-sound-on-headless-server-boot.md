---
title: "Playing a Sound on Headless Server Boot"
date: 2023-12-21T16:30:48-05:00
draft: false
author: "Victor Mendonça"
description: "How to configure bootup sounds on Ubuntu"
tags: ["Linux", "Ubuntu"]
---

Beep is a versatile utility that transforms your computer speaker into a musical instrument reminiscent of early Nintendo-style sounds. You provide a frequency and length and beep will play the respective sound.

I started using beep in the early 2000s with [Smoothwall](https://en.wikipedia.org/wiki/Smoothwall) (think of a Linux predecessor to pfSense) to notify me when it had rebooted. That is still my main use for the utility (less the Smoothwall part), but it can be used for many other things. 

We'll go through a quick setup on Ubuntu using group permission, but you can refer to the main GitHub repo ([Permission setup for beep](https://github.com/spkr-beep/beep/blob/master/PERMISSIONS.md)) for alternative setup instructions if you need something different.

### Configuration

Let's start by installing beep:

```bash
sudo apt update && sudo apt install beep
```

Now create the `beep` system group. Any user member of this group will be able to run beep.

```bash
sudo addgroup --system beep
```

Add it as a secondary group for your user:

```bash
sudo usermod [user] -a -G beep
```

Create the udev rule to allow the group permission to the speaker:

`/usr/lib/udev/rules.d/90-pcspkr-beep.rules`

```
# Add write access to the PC speaker for the "beep" group
ACTION=="add", SUBSYSTEM=="input", ATTRS{name}=="PC Speaker", ENV{DEVNAME}!="", GROUP="beep", MODE="0620"
```

Comment out the existing blacklist for 'pcspkr' in `/etc/modprobe.d/blacklist.conf`:

```
# ugly and loud noise, getting on everyone's nerves; this should be done by a
# nice pulseaudio bing (Ubuntu: #77010)
# blacklist pcspkr
```

Load the module:

```bash
sudo modprobe pcspkr
```

Now let's test to make sure that it works. You will need to logout and log back in, or simply ssh to your localhost (this is so the secondary group is loaded). And then run beep with the options below:

```bash
beep -f 587 -l 714
```

![](/img/playing-a-sound-on-headless-server-boot/beep.jpg)

If that works then we have confirmed that we configured everything correctly. Just a reminder, your computer needs to have a speaker, otherwise beep wont work. 

Now let's create a sample script and the Systemd unit file for the service that will play beep after boot. Create the following script and give it execute permission:

`/usr/local/bin/star-trek.sh`

```bash
#!/bin/bash
beep -f 587 -l 714
beep -f 784 -l 238
beep -f 1046 -l 1428
beep -f 987 -l 476
beep -f 784 -l 357
beep -f 659 -l 357
beep -f 880 -l 357
beep -f 1174 -l 952
```

```bash
sudo chmod +x /usr/local/bin/star-trek.sh
```

Create  `/lib/systemd/system/beep-startup.service` with the following content. Note that you will need to change the `User=` property to be the username for your user:

```systemd
# /lib/systemd/system/beep-startup.service
[Unit]
Description=Plays startup audio
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/local/bin/star-trek.sh
User=[user]

[Install]
WantedBy=default.target
```

Let's enable and start the service, and it should play the Star Trek intro sound:

```bash
systemctl enable --now beep-startup.service
```

![](/img/playing-a-sound-on-headless-server-boot/star-trek.jpg)

### Conclusion

With these configurations, your machine will serenade you with the iconic Star Trek intro sound after establishing a network connection during boot. Dive into the world of beep, and if you're looking for more scripts, explore [GitHub - victorbrca/beep-scripts](https://github.com/victorbrca/beep-scripts) for an extensive collection. Happy beeping!

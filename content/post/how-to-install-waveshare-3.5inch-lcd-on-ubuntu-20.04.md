---
title: "How to Install Waveshare 3.5\" LCD on Ubuntu 20.04"
date: 2020-10-13T14:45:00-04:00
draft: false
author: "Victor Mendonça"
description: "Instructions on getting a 3.5\" TFT LCD working on a RPI3B with Ubuntu 20.04 64-bit"
tags: ["Linux", "Hardware", "Raspberry Pi"]
---

Following the steps of an older post ([Installing Kuman 3](https://blog.victormendonca.com/2018/06/26/installing-kuman-3.5-tft-lcd-on-raspberry-pi-3-model-b/)), I find myself trying to configure the same Waveshare 3.5" LCD touch screen on my Raspberry Pi 3B, but now with Ubuntu 20.04 64.bit.

As usual the LCDs from Waveshare are not that easy to configure. While researching online I could not find any working instructions on how to configure the screen with Ubuntu 20.04. I found a few Git repos ([Wavesahre](https://github.com/waveshare/LCD-show) and [LCD Wiki](https://github.com/lcdwiki/LCD-show-ubuntu)), but they all failed to get me with a working config.

After spending a lot of time I was able to get it to work using some of the files and instructions from Waveshare's official Git Repo (as well as LCD Wiki).

If you want to save yourself sometime, just use my Ansible repo to get your RPI3B configured. Otherwise, the manual instructions are bellow.

[GitHub: waveshare35-rpi3b-ubuntu-20.04-64](https://github.com/victorbrca/waveshare35-rpi3b-ubuntu-20.04-64)

Instructions
---

a. Download Ubuntu 20.04 64-bit for RPI3 and setup your SD card

https://ubuntu.com/download/raspberry-pi

![](img/how-to-install-waveshare-3.5inch-lcd-on-ubuntu-20.04/download-ubuntu.png)

Make sure to check the downloaded file

![](img/how-to-install-waveshare-3.5inch-lcd-on-ubuntu-20.04/verify-download.png)

b. Power the Pi on, login (SSH or HDMI), change the password and update

```none
# apt-get update apt-get upgrade
```

c. Install Xubuntu and evdev

_**Note:** you could just install X instead of Xubuntu and there's a chance the instructions will work_

```none
# apt-get install xubuntu-desktop xserver-xorg-input-evdev
```

d. Backup the files we are going to modify

_We are not modifying `config.txt`, but we are backing it up just in case_

```none
# cd /boot/firmware

# cp cmdline.txt cmdline.txt.orig
# cp usercfg.txt usercfg.txt.orig
# cp config.txt config.txt.orig
```

e. Add the following lines to `/boot/firmware/usercfg.txt`

```none
dtparam=i2c_arm=on
dtparam=audio=on
dtparam=spi=on
enable_uart=1
dtoverlay=waveshare35a
hdmi_drive=2
disable_overscan=1
```

f. Make changes to `/boot/firmware/cmdline.txt`

+ Modify
  + console=ttyAMA0,115200
+ Add
  + fbcon=map:10
  + fbcon=font:ProFont6x11

```none
net.ifnames=0 dwc_otg.lpm_enable=0 console=ttyAMA0,115200 console=tty1 root=LABEL=writable rootfstype=ext4 elevator=deadline rootwait fixrtc fbcon=map:10 fbcon=font:ProFont6x11
```

g. Create the directory `/etc/X11/xorg.conf.d`

```none
# mkdir -p /etc/X11/xorg.conf.d
```

h. Create the file `/etc/X11/xorg.conf.d/99-calibration.conf`

```none
Section "InputClass"
    Identifier "calibration"
    MatchProduct "ADS7846 Touchscreen"
    Option "Calibration" "3932 300 294 3801"
    Option "SwapAxes" "1"
    Option "EmulateThirdButton" "1"
    Option "EmulateThirdButtonTimeout" "750"
    Option "EmulateThirdButtonMoveThreshold" "30"
EndSection
```

i. Create the file `/usr/share/X11/xorg.conf.d/99-fbturbo.conf`

```none
Section "Device"
    Identifier "Allwinner A10/A13 FBDEV"
    Driver "fbturbo"
    Option "fbdev" "/dev/fb2"

    Option "SwapbuffersWait" "true"
EndSection
```

j. Copy `10-evdev.conf` to `45-evdev.conf`

```none
# cd /usr/share/X11/xorg.conf.d/
# cp 10-evdev.conf 45-evdev.conf
```

k. Clone `https://github.com/waveshare/LCD-show.git`

```none
# mkdir /root/Git
# cd /roo/Git

# git clone https://github.com/waveshare/LCD-show.git

# cd LCD-show
```

l. Copy `waveshare35a-overlay.dtb` to `/boot/firmware/overlays` as both `waveshare35a-overlay.dtb` and `waveshare35a-overlay.dtbo`

```none
# cp waveshare35a-overlay.dtb /boot/firmware/overlays/waveshare35a-overlay.dtb
# cp waveshare35a-overlay.dtb /boot/firmware/overlays/waveshare35a-overlay.dtbo
```

m. Reboot and enjoy

![](https://pbs.twimg.com/media/Ej7nEdZWAAIZ9f3?format=jpg&name=medium)

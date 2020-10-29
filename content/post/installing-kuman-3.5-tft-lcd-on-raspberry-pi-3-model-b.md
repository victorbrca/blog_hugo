---
title: "Installing Kuman 3"
date: 2018-06-26T18:36:26-04:00
draft: false
author: "Victor Mendonça"
description: "Simple instructions to install Kuman 3.5 TFT LCD on Raspberry Pi"
tags: ["Linux", "Hardware"]
---

Simple instructions (assuming a new Raspbian install) to install [Kuman 3.5 TFT LCD](https://www.amazon.ca/gp/product/B01FXCFA2M/ref=oh_aui_search_detailpage?ie=UTF8&psc=1) on Raspberry Pi 3 Model B.

a. Download [Rasbian strech](https://www.raspberrypi.org/downloads/raspbian/) (with desktop) and extract the `.img` file from the downloaded zip (it's always good to also check the hash of the downloaded file)

b. Insert the SD card and make note of the device being used (**make sure you get this right not to overwrite your OS**)

c. Use `dd` to copy the image to the SD card (the values for `if` and `of` should be changed accordingly. If you never used `dd` please do yourself a favor and read up first)

```
sudo dd bs=4M if=2018-04-18-raspbian-stretch.img of=/dev/mmcblk0 status=progress oflag=sync
```

d. Once the process is done, mount (if needed) the `boot` and `rootfs` partitions

e. Browse to the mount point of `boot` and create a blank file called `ssh` (to enable sshd on boot)

```
touch ssh
```

f. Open `cmdline.txt` and write down the value of `root=`. Now substitute the existing line with the line below, and then change the value of `root=` back to the original value

```
dwc_otg.lpm_enable=0 console=tty1 console=ttyAMA0,115200 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait fbcon=map:10 fbcon=font:ProFont6x11 logo.nologo
```

g. Open `config.txt` and add the lines below to the end of the file

```
dtparam=audio=on
dtparam=spi=on
dtoverlay=ads7846,penirq=25,penirq_pull=2,xohms=150,swapxy=1,xmin=300,ymin=700,xmax=3800,ymax=3400,pmax=255
dtoverlay=waveshare35a
```

h. Download `waveshare35a-overlay.dtb` from [GitHub](https://github.com/swkim01/waveshare-dtoverlays) to `/boot/overlays` and rename it to `waveshare35a.dtbo`

i. Change `Option "fbdev" "/dev/fb0"` in `/usr/share/X11/xorg.conf.d/99-fbturbo.conf` to `Option "fbdev" "/dev/fb1"`

```
Section "Device"
    Identifier "Allwinner A10/A13 FBDEV"
    Driver "fbturbo"
    Option "fbdev" "/dev/fb1"

    Option "SwapbuffersWait" "true"
EndSection
```

j. Create a new file `/etc/X11/xorg.conf.d/99-calibration.conf` with the lines below

```
Section "InputClass"
    Identifier "calibration"
    MatchProduct "ADS7846 Touchscreen"
    Option "Calibration" "3932 300 294 3801"
    Option "SwapAxes" "1"
EndSection
```

k. Reboot and enjoy

![](https://i.imgur.com/Gvtpp8f.jpg)

* * *

**References**

* [GitHub](https://github.com/goodtft/LCD-show/issues/17)
* [Amazon](https://www.amazon.com/review/R3CGCFPLQ232W2/ref=cm_cr_dp_title?ie=UTF8&ASIN=B01CNJVG8K&channel=detail-glance&nodeID=541966&store=pc)

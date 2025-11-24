---
title: "How to Configure Waveshare 3.5\" LCD on Rapberry Pi 3B and Debian Trixie"
date: 2025-11-24T10:38:32-05:00
draft: false
author: "Victor Mendonça"
description: "A step-by-step guide to configuring and calibrating the Waveshare 3.5\" LCD on a Raspberry Pi 3B running Debian Trixie."
tags: ["Linux", "Hardware", "Raspberry Pi"]
---

![](/img/how-to-configure-waveshare-3.5-lcd-on-rapberry-pi-3b-and-debian-trixie/header.png)

The last time I had to configure the Waveshare 3.5" LCD on my Raspberry Pi 3B (see [How to Install Waveshare 3.5" LCD on Ubuntu 20.04](https://blog.victormendonca.com/2020/10/13/how-to-install-waveshare-3.5inch-lcd-on-ubuntu-20.04/)) I ran into a lot of issues. I even automated the install/setup so I could replicate it if needed, saving me from having to deal with the over-complicated process.

Last week I had to re-image my Raspberry Pi 3B, that I use as my UPS controller, due to a faulty SD card. And to my surprise, the install and configuration of the LCD screen was another fiasco. The instructions on the [Waveshare Wiki](https://www.waveshare.com/wiki/3.5inch_RPi_LCD_(A)#For_All_Raspberry_Pi_Versions) don't work, and made me very frustated.

So to save you some time, and hair (it's too late for me as I'm already bald), I have outlined the steps that worked for me.

## Configure the Driver and OS

a. Update the OS

```bash
sudo apt update
```

b. Download the driver and copy it to `/boot/overlays/`

```bash
wget https://files.waveshare.com/wiki/common/Waveshare35a.zip
unzip ./Waveshare35a.zip
sudo cp waveshare35a.dtbo /boot/overlays/
```

c. Install the needed packages

```bash
sudo apt install --no-install-recommends xserver-xorg xinit lightdm -y
sudo apt install raspberrypi-ui-mods xserver-xorg-video-fbturbo -y
```

d. Configure `/boot/firmware/config.txt`

Comment out:

```ini
# Enable DRM VC4 V3D driver
#dtoverlay=vc4-kms-v3d            <---
#max_framebuffers=2               <---
```

Add:

```ini
[all]                # <--- Already exists

dtparam=spi=on
dtoverlay=waveshare35a
display_rotate=0
```

e. Disable `99-fbturbo.~` and `20-noglamor.conf`

```bash
cd /usr/share/X11/xorg.conf.d/
sudo mv 99-fbturbo.~ 99-fbturbo.bak
sudo mv 20-noglamor.conf 20-noglamor.conf.bak
```

f. Create `/usr/share/X11/xorg.conf.d/99-fbturbo.conf`

```conf
Section "Device"
        Identifier      "Waveshare SPI LCD"
        Driver          "fbdev"
        Option          "fbdev" "/dev/fb1"
        Option          "SwapbuffersWait" "true"
EndSection
```

g. Fix Console/Splash Screen Mapping `/boot/firmware/cmdline.txt`

Append to end of the line:

```ini
fbcon=map:1
```

h. Disable lightdm

```bash
sudo systemctl disable lightdm.service
```

i. Set Console Autologin

```bash
sudo raspi-config nonint do_boot_behaviour B2
```

#### Configure Auto-Login for the Default User

This is the default user (with GID 1000), and it's usually the 'pi' user. You can check the output of `id`, and the user set in `/etc/systemd/system/getty@tty1.service.d/autologin.conf`.

_Here you can see that 'johndoe' is configured:_

```bash
cat /etc/systemd/system/getty@tty1.service.d/autologin.conf
```

```ini
[Service]
ExecStart=
ExecStart=-/sbin/agetty --autologin johndoe --noclear %I $TERM
```

a. Add to `~/.bash_profile`

```bash
startx  2> /tmp/xorg_errors
```

b. Add the video and tty groups

```bash
sudo usermod -aG tty,video [USER]
```

#### Reboot

```bash
reboot
```

#### Calibrate the Display

We will now calibrate the display to make sure that touches work properly.

a. Install the needed packages:

```bash
sudo apt install xserver-xorg-input-evdev xinput-calibrator -y
```

b. On the raspberry pi, run `xinput_calibrator` and touch the red dots. You can output to a file if you prefer to configure on another terminal after (e.g.: ssh)

```bash
xinput_calibrator > output_file
```

c. Copy the output to `/etc/X11/xorg.conf.d/99-calibration.conf`

```none
$ xinput_calibrator

	Calibrating standard Xorg driver "ADS7846 Touchscreen"
		current calibration values: min_x=0, max_x=65535 and min_y=0, max_y=65535
		If these values are estimated wrong, either supply it manually with the --precalib option, or run the 'get_precalib.sh' script to automatically get it (through HAL).
		--> Making the calibration permanent <--
	  copy the snippet below into '/etc/X11/xorg.conf.d/99-calibration.conf' (/usr/share/X11/xorg.conf.d/ in some distro's)
	Section "InputClass"
		Identifier	"calibration"
		MatchDriver	"evdev"
		Option	"MinX"	"21663"
		Option	"MaxX"	"22027"
		Option	"MinY"	"48912"
		Option	"MaxY"	"49185"
		Option	"SwapXY"	"1" # unless it was already set to 1
		Option	"InvertX"	"0"  # unless it was already set
		Option	"InvertY"	"0"  # unless it was already set
	EndSection
```

_`/etc/X11/xorg.conf.d/99-calibration.conf`_

```
Section "InputClass"
	Identifier	"calibration"
	MatchDriver	"evdev"
	Option	"MinX"	"21663"
	Option	"MaxX"	"22027"
	Option	"MinY"	"48912"
	Option	"MaxY"	"49185"
	Option	"SwapXY"	"1" # unless it was already set to 1
	Option	"InvertX"	"0"  # unless it was already set
	Option	"InvertY"	"0"  # unless it was already set
EndSection
```

_**Note:** Don't copy my values. You should use the ones from your output._

d. Reboot

#### Conclusion

That’s it! You should now have a working X11 desktop on your 3.5” LCD. If you ran into any specific issues with Debian Trixie that I missed, or if you have different calibration values, feel free to drop a comment below.
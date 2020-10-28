---
title: "How to Setup a USB UPS on pfSense"
date: 2020-10-28T16:16:10-04:00
draft: false
author: "Victor Mendonça"
description: "Guide on configuring a USB UPS in pfSense"
tags: ["pfSense", "Hardware"]
---

So you need to configure pfSense with an UPS!? Well, good thing this post is called "**_How to Setup UPS on pfSense_**".

a. Start by plugging the USB cable to your pfSense and your UPS

b. Now log in to the pfSense UI and go into "System => Package Manager"

![](./img/how-to-setup-ups-on-pfsense/menu-1.png)

b. Search for 'nut' and click on 'Install'

![](./img/how-to-setup-ups-on-pfsense/1.package.png)

c. Go to "Services => UPS => UPS Settings", select "Local USB", give the UPS a name and click on "Save"

![](./img/how-to-setup-ups-on-pfsense/ups-settings.png)

d. Go back to the "UPS Status" page. If you can see your UPS then you are pretty much done. Now all you have to do is configure any additional NUT settings (if you need).

![](./img/how-to-setup-ups-on-pfsense/ups-status.png)

- - -

Possible Issues
---

### Troubleshooting 1

If your UPS is not showing in the "UPS Settings" page, logon to pfSense via ssh and issue `usbconfig`. You should be able to see your UPS listed.

```none
[2.4.4-RELEASE][admin@pfSense.localdomain] usbconfig
ugen0.1: <0x8086 XHCI root HUB> at usbus0, cfg=0 md=HOST spd=SUPER (5.0Gbps) pwr=SAVE (0mA)
ugen1.1: <Intel EHCI root HUB> at usbus1, cfg=0 md=HOST spd=HIGH (480Mbps) pwr=SAVE (0mA)
ugen0.2: <Smart Smart Wireless Device> at usbus0, cfg=0 md=HOST spd=FULL (12Mbps) pwr=ON (100mA)
ugen1.2: <vendor 0x8087 product 0x8000> at usbus1, cfg=0 md=HOST spd=HIGH (480Mbps) pwr=SAVE (0mA)
ugen0.3: <American Power Conversion Back-UPS ES 750G FW908.W3 .D USB FWW3> at usbus0, cfg=0 md=HOST spd=LOW (1.5Mbps) pwr=ON (2mA)
```

#### Solution:

If you can't see your UPS with `upsconfig` try using another USB cable.

### Troubleshooting 2

If you can see the UPS with `usbconfig`, try to restart the service from command line so you can view error messages on stdout.

Browse to `/usr/local/etc/rc.d` and manually restart the NUT service with `./nut.sh restart`.

```none
[2.4.4-RELEASE][admin@pfSense.localdomain]/usr/local/etc/rc.d: ./nut.sh restart
stopping NUT
starting NUT
Network UPS Tools upsmon 2.7.4
kill: No such process
UPS: BackUPSES750 (master) (power value 1)
Using power down flag file /etc/killpower
Network UPS Tools - UPS driver controller 2.7.4
Network UPS Tools - Generic HID driver 0.41 (2.7.4)
USB communication driver 0.33
No matching HID UPS found
Driver failed to start (exit status=1)
Network UPS Tools upsd 2.7.4
fopen /var/db/nut/upsd.pid: No such file or directory
listening on ::1 port 3493
listening on 127.0.0.1 port 3493
Can't connect to UPS [BackUPSES750] (usbhid-ups-BackUPSES750): No such file or directory

Broadcast Message from admin@pfSense.localdomain                               
        (no tty) at 16:57 EDT...                                               

Communications with UPS BackUPSES750 lost                                      


Broadcast Message from admin@pfSense.localdomain                               
        (no tty) at 16:57 EDT...                                               

UPS BackUPSES750 is unavailable
```

> 💡 ERROR
> `Can't connect to UPS [BackUPSES750] (usbhid-ups-BackUPSES750): No such file or directory`

#### Solution:

Restart pfSense

### Troubleshooting 3

Try running `upsc [ups name]`.

```none
[2.4.4-RELEASE][admin@pfSense.localdomain] upsc BackUPSES750
Error: Driver not connected

Broadcast Message from admin@pfSense.localdomain                               
        (no tty) at 17:02 EDT...                                               

UPS BackUPSES750 is unavailable
```

> 💡 ERROR
> `Error: Driver not connected`

#### Solution:

Restart pfSense

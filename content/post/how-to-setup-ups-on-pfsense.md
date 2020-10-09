---
title: "How to Setup UPS on pfSense"
date: 2020-10-07T19:29:10-04:00
draft: true
author: "Victor Mendonça"
description: "Guide on configuring pfSense with a UPS"
tags: ["Linux", "pfSense", "Networking"]
---


https://networkupstools.org/stable-hcl.html

https://forum.netgate.com/topic/141802/poll-ups-apc-smart-ups-1500-failed-driver-not-connected
https://networkupstools.org/docs/man/index.html
https://blog.lbdg.me/n-u-t-ups-monitoring-via-pfsense-grafana/
https://wiki.archlinux.org/index.php/Network_UPS_Tools


```
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


```
[2.4.4-RELEASE][admin@pfSense.localdomain]/usr/local/etc/rc.d: usbconfig
ugen0.1: <0x8086 XHCI root HUB> at usbus0, cfg=0 md=HOST spd=SUPER (5.0Gbps) pwr=SAVE (0mA)
ugen1.1: <Intel EHCI root HUB> at usbus1, cfg=0 md=HOST spd=HIGH (480Mbps) pwr=SAVE (0mA)
ugen0.2: <Smart Smart Wireless Device> at usbus0, cfg=0 md=HOST spd=FULL (12Mbps) pwr=ON (100mA)
ugen1.2: <vendor 0x8087 product 0x8000> at usbus1, cfg=0 md=HOST spd=HIGH (480Mbps) pwr=SAVE (0mA)
ugen0.3: <American Power Conversion Back-UPS ES 750G FW908.W3 .D USB FWW3> at usbus0, cfg=0 md=HOST spd=LOW (1.5Mbps) pwr=ON (2mA)
```

`upsc BackUPSES750`

```
[2.4.4-RELEASE][admin@pfSense.localdomain]/usr/local/etc/rc.d: upsc BackUPSES750
Error: Driver not connected

Broadcast Message from admin@pfSense.localdomain                               
        (no tty) at 17:02 EDT...                                               

UPS BackUPSES750 is unavailable
```

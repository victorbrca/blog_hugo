---
title: "How to Configure a UPS Between Linux and FreeNAS"
date: 2020-10-13T12:01:12-04:00
draft: true
author: "Victor Mendonça"
description: "Configure a UPS Between Linux and FreeNAS with NUT"
tags: ["Linux", "Shell", "Hardware", "FreeNAS"]
---

After having bad memory modules due to power outages at home (on both my FreeNAS and my desktop) I knew I had to invest on a UPS. I will cover the details of the configuration, and as usual, I will try to go straight to the point.

Keep in mind that I'm running Arch on my desktop and FreeNAS-11.3-U1, and that the UPS is connected to my desktop (master) via USB.

Instructions
---

### Hardware

a. Connect the USB cable to your computer

b. Make sure that you can see it with `lsusb`

```none
➤ lsusb | grep -i ups
Bus 002 Device 008: ID 0764:0501 Cyber Power System, Inc. CP1500 AVR UPS
```

### Desktop

#### Configuring the UPS

a. Start by installing `nut` and `nut-monitor` (if you want a nice GUI to view UPS status)

```none
local/nut 2.7.4-2
    A collection of programs which provide a common interface for monitoring and administering UPS, PDU and SCD
    hardware

local/nut-monitor 2.7.4-3
    GUI to manage devices connected a NUT server
```

b. Next run `nut-scanner` to see if identifies your UPS. This will give a start configuration that we can use for the UPS

```none
➤ sudo nut-scanner -U
Scanning USB bus.
[nutdev1]
	driver = "usbhid-ups"
	port = "auto"
	vendorid = "0764"
	productid = "0501"
	product = "SL Series"
	vendor = "CPS"
	bus = "002
```

c. Edit `/etc/nut/ups.conf` and add the output from the previous step. You can change the device name (between `[]`) to anything you like

```none
[CyberPowerSL700U]
	driver = "usbhid-ups"
	port = "auto"
	vendorid = "0764"
	productid = "0501"
	product = "SL Series"
	vendor = "CPS"
	bus = "002"
```

d. Start the USB driver

```none
➤ sudo upsdrvctl start
```

_d.1._ If you get an error here you might need to setup a udev rule for your UPS. This can be done by creating the file `/etc/udev/rules.d/50-ups.rules` with the content below

> `SUBSYSTEM=="usb", ATTR{idVendor}=="XXXX", ATTR{idProduct}=="YYYY", SYMLINK+="ups0", GROUP="nut"`

Make sure to add the vendor ID and product ID for the UPS (both `lsusb` and `nut-scanner -U` can provide that info).

```none
➤ cat /etc/udev/rules.d/50-ups.rules
SUBSYSTEM=="usb", ATTR{idVendor}=="0764", ATTR{idProduct}=="0501", SYMLINK+="ups0", GROUP="nut"
```

_d.2._ After that reload udev rules and try to start the UPS USB driver again

```none
➤ sudo udevadm control --reload-rules && sudo udevadm trigger
➤ sudo upsdrvctl start
```

e. Make sure that you can view the UPS status with `upsc`

```none
➤ upsc CyberPowerSL700U
battery.charge: 100
battery.charge.low: 10
battery.charge.warning: 20
battery.mfr.date: CPS
battery.runtime: 599
battery.runtime.low: 300
battery.type: PbAcid
battery.voltage: 14.2
battery.voltage.nominal: 12
device.mfr: CPS
device.model: SL Series
device.type: ups
driver.name: usbhid-ups
driver.parameter.bus: 002
driver.parameter.pollfreq: 30
driver.parameter.pollinterval: 2
driver.parameter.port: auto
driver.parameter.product: SL Series
driver.parameter.productid: 0501
driver.parameter.synchronous: no
driver.parameter.vendor: CPS
driver.parameter.vendorid: 0764
driver.version: 2.7.4
driver.version.data: CyberPower HID 0.4
driver.version.internal: 0.41
input.transfer.high: 140
input.transfer.low: 96
input.voltage: 115.0
input.voltage.nominal: 0
output.voltage: 115.0
ups.beeper.status: enabled
ups.delay.shutdown: 20
ups.delay.start: 30
ups.load: 44
ups.mfr: CPS
ups.model: SL Series
ups.productid: 0501
ups.realpower.nominal: 375
ups.status: OL
ups.timer.shutdown: -60
ups.timer.start: -60
ups.vendorid: 0764
```

#### Configuring the NUT Server

a. Configure it as the master (meaning that it will send the shutdown command to the listening clients when the battery is low). Edit `/etc/nut/nut.conf` and set the `MODE` to `netserver`

```none
MODE=netserver
```

b. Now let's configure the listening IP and port. Edit `/etc/nut/upsd.conf` and make sure the localhost and the real IP are set with the `LISTEN` directive (and that is uncommented)

_Assuming that my desktop IP is `192.168.10.10`_
```none
LISTEN 127.0.0.1 3493
LISTEN 192.168.10.10 3493
```

c. Configure the users in `/etc/nut/upsd.users`

_Add passwords under `my_master_password` and `my_slave_password`_

```none
[upsmaster]
  password = my_master_password
  upsmon master

[upsremote]
  password = my_slave_password
  upsmon slave
```

d. Now let's tell `upsmon` to monitor the UPS. Edit `/etc/nut/upsmon.conf` with the lines below

_Make sure to change the UPS name (`ups_name`) and master password (`my_master_password`)_

```
MONITOR ups_name@localhost 1 upsmaster my_master_password master
```

e. Check the status of the NUT server with `systemctl status nut-server`

![](img/how-to-configure-a-ups-between-linux-and-freenas/status.png)

f. Enable the NUT server and monitor

```none
➤ sudo systemctl enable --now nut-server.service
➤ sudo systemctl enable --now nut-monitor.service
```


### Configuring FreeNAS as the client

a. Login to your FreeNAS and go to Services. Enable the UPS service, set it to start automatically and then click on the edit button

![](img/how-to-configure-a-ups-between-linux-and-freenas/freenas.1.png)

b. Make the following configuration



c. Check the connection to the UPS with the command below

> `upsc ups_name@ip:port`

![](img/how-to-configure-a-ups-between-linux-and-freenas/freenas.3.png)

d. Check that FreeNAS is actually monitoring the UPS (`/var/log/nut/ups.log`)

> OL - On line (no power failure) (opposite of OB - on battery)
>
> LB - Low battery

![](img/how-to-configure-a-ups-between-linux-and-freenas/freenas.4.png)

e. Log back to your desktop and confirm that you can see your FreeNAS as a connected client (`upsc -c`)

_Assuming that my FreeNAS IP is `192.168.10.20`_
```
$ upsc -c ups_name@localhost
192.168.10.20
```

### Testing

Now let's test that is all works. Instead of pulling the cord we can do this by running `upsmon -c fsd`. This will save wear and tear on your battery.

```
➤ sudo upsmon -c fsd
```

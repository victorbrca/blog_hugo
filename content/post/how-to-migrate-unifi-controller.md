---
title: "How to Migrate Unifi Controller"
date: 2019-01-14T12:57:11-05:00
draft: false
author: "Victor Mendonça"
description: "Instructions on how to migrate a Unifi controller on Linux"
tags: ["Linux", "Networking"]
---

Quick instructions on how to migrate a Unifi controller on Linux. Note that it requires SSH access to the AP and a bit of downtime.

a. Logon to your old Unifi controller, go to `Settings=>Auto Backup` and download a backup

**Note:** _Force a new backup if you have new changes_

![](../img/how-to-migrate-unifi-controller/img1.png)

b. Browse to your AP, write down the IP address

c. Select the AP, go to `Config=>Manage Device=>Forget this device` and click on `Forget` (click ok on the alert)

d. Login to the new controller and on the first screen restore the backup you save on step `a`

e. Once the controller is back up, SSH into the device's AP with the default user (`ubnt:ubnt` or `root:ubnt`) and run the following command (change `[controler_ip] for the IP of your controller`)

```
set-inform http://[controller-ip]:8080/inform
```

f. On the new controller, under devices, the AP should be showing for 'adoption'. Click on `Adopt`

![](../img/how-to-migrate-unifi-controller/img2.png)

---
title: "TLP and CPUFreq on ThinkPad P1 Gen 2 (KDE Arch)"
date: 2020-06-17T20:00:36-04:00
draft: false
author: "Victor Mendonça"
description: "How to setup TLP and CPUFreq on a ThinkPad P1 Gen 2 running KDE Arch"
tags: ["Linux", "Hardware", "ThinkPad"]
---

On this tutorial I will show you how to install and configure `TLP` and `Intel P-state and CPUFreq Manager` on your ThinkPad P1 Gen 2 (with KDE Arch).

If you don't know, _**TLP**_ allows you to configure specific rules to help optimize the battery life on your laptop, while _**Intel P-state and CPUFreq Manager**_ gives you a pretty interface via a tray icon that allows you to control CPU/GPU frequencies and a few power profiles.

TLP
---

Using TLP's threshold functionality we can change the charge thresholds for the battery. On ThinkPads the charging process is controlled by the embedded controller (EC) firmware (instead of software running on the operating system). Lenovo's default settings start charging when the battery drops below 96%, and stops at 100%. This is good for "performance" but it deteriorates the battery causing a shorter lifespan. By changing the battery charge thresholds with TLP we can extend the lifespan of the battery.

_**Note:** Lithium-ion batteries do not suffer from memory effect like NiCd and NiMH batteries. See the quote below (https://batteryuniversity.com) for an explanation_

> A lithium-ion battery provides 300-500 discharge/charge cycles. The battery prefers a partial rather than a full discharge. Frequent full discharges should be avoided when possible. Instead, charge the battery more often or use a larger battery. There is no concern of memory when applying unscheduled charges.

### Installation

This part is very simple. Install the following packages and then reboot:

**=> Main**

+ `tlp`
+ `acpi_call`
+ `smartmontools`

**=> AUR**

+ `tlpui-git`

### Configuration

a. Run `tlpui` and go to the 'ThinkPad Battery' tab on the left

b. Set the following parameters/options

##### `START_CHARGE_THRESH_BAT0`

_Value = 50/60_

This is the threshold of when the battery will start charging. If you set it to 50 the battery will only start charging when it's below 50%.

##### `STOP_CHARGE_THRESH_BAT0`

_Value = 70/80_

This is the value of when the battery will stop charging. If you set it to 80 the battery will stop charging when close to %80.

##### `RESTORE_THRESHOLDS_ON_BAT`

_Value = enabled_

When you bypass the charge thresholds with a TLP command you would usually need to reboot your machine to reset the thresholds. When `RESTORE_THRESHOLDS_ON_BAT` is enabled the configured thresholds will be restored when the power is unplugged.

This is useful if you need to fully charge your battery for a meeting, or to work in a place where you know you won't have a power outlet.

##### `NATACPI_ENABLE`

_Value = enabled_

##### `TPACPI_ENABLE`

_Value = enabled_

##### `TPSMAPI_ENABLE`

_Value = disabled_

`tp_smapi` doesn't support newer models, so we need to disable this.

c. Go back to the 'General' tab and enable `TLP_ENABLE`

d. Click on 'save' and reboot

### Additional TLP Commands

**Get a full report from TLP**

```none
sudo tlp-stat
```

**Get a report with battery information only**

```none
sudo tlp-stat -b
```

**Temporarily bypass the current config and use specified threshold**

```none
sudo tlp setcharge [start threshold] [stop threshold]
```

**Bypass thresholds and fully charge battery**

```none
sudo tlp fullcharge
```

**Bypass the start threshold and charge up to the stop threshold**

```none
sudo tlp chargeonce
```

Intel P-state and CPUFreq Manager
---

a. Install `plasma5-applets-plasma-pstate` (AUR)

b. Add the widget to your panel

![](/img/lenovo-p1-gen-tlp-on-arch/add_widget.png)

c. Done! You should now be able to use the widget for basic control

![](/img/lenovo-p1-gen-tlp-on-arch/widget_view.png)

- - -

Reference:

+ https://linrunner.de/tlp/faq/battery.html
+ https://support.lenovo.com/ca/en/solutions/ht078208
+ https://wiki.archlinux.org/index.php/TLP
+ https://store.kde.org/p/1282623/

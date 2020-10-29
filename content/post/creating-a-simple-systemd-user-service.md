---
title: "Creating a Simple Systemd User Service"
date: 2018-05-14T12:53:43-04:00
draft: false
author: "Victor Mendonça"
description: "Tutorial on creating a simple systemd service"
tags: ["Linux", "Systemd"]
---

Quick instructions on how to create a simple systemd user service for a program or script.

1- Identify the script or program/binary that you will be using

2- Create a systemd unit file using the example below, give it a name that will make sense to you with a `.service` extension (like `[my_service].service`), and save it to `$USER/.config/systemd/user`

```
[Unit]
Description=[Service description]

[Service]
Type=simple
StandardOutput=journal
ExecStart=[script path]

[Install]
WantedBy=default.target
```

For this example we used a service type simple, which allows systemd to take care of the most basic needs for us. The options used are:

* **Description** = Description of your service. This will be shown when handling your service with `systemctl`
* **Type** = We will be using the simple type, and this could be left out
* **StandardOutput** = We will be logging it to the system log (you can use `journalctl` to view it)
* **ExectStart** = The script or program to be executed
* **WantedBy** = Service will be run using the default target. You can find what the default.target is with `systemctl get-default`

3- Enable your service so it starts automatically

```
systemctl --user enable [my_service].service
```

4- Start the service

```
systemctl --user start [my_service].service
```

4- Check that it's running

```
systemctl --user status [my_service].service
```

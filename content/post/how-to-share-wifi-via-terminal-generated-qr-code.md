---
title: "How to Share WiFi Credentials from Terminal"
date: 2020-04-23T20:24:12-04:00
draft: false
author: "Victor Mendonça"
description: "Instructions on how to create a QR code to share your WiFi"
tags: ["Bash", "Linux", "Networking"]
---

Now here's a cool and quick way to share your WiFi SSID and password from a terminal window with a guest.

You can use `qrencode` to generate a QR code with the parameters below:

```none
WIFI:S:[Your SSID here];T:WPA;P:[Your Password Here];;
```

For example, let's pretend my SSID is `MySweetSSID` and my password is `mysecretpassword`. We can run:

```
qrencode -o - -t utf8 'WIFI:S:MySweetSSID;T:WPA;P:mysecretpassword;;'
```

To get:

![](/img/how-to-share-wifi-via-terminal-generated-qr-code/2020-04-23_20-18.png)

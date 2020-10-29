---
title: "How to Install chrome-remote-desktop on Arch"
date: 2020-04-02T19:03:03-04:00
draft: false
author: "Victor Mendonça"
description: "Instructions on how to install and configure chrome-remote-desktop on Arch"
tags: ["Linux", "Arch"]
---

Chrome Remote Desktop has been around for quite a while, but now Google offers a `.deb` installer with native Linux support via Systemd. This is great because it removes the need to setup VPNs and VNC to remote connect to your machines, or in the case that you need to land a hand to a not so technical savvy family member or friend.

Unfortunately the installer is only for Ubuntu (and Debian based distros), but with a few steps we can get it running on Arch, and (thanks to a patch by [nightuser](https://gist.github.com/nightuser)) even configure it to use existing X sessions instead of creating a new one (which is the default behavior).

As expected, the packages exists in the AUR, so the install should be pretty simple.

Instructions
---

### Install

a. Install `chrome-remote-desktop` from the AUR

b. Run `crd --setup` to configure your connection. Hit any key

![](/img/how-to-install-chrome-remote-desktop-on-arch/1_prompt-1.png)

c. Select your Desktop Environment (I selected KDE which is what I use) and save the file

![](/img/how-to-install-chrome-remote-desktop-on-arch/2_select_de.png)

d. Press any key again

![](/img/how-to-install-chrome-remote-desktop-on-arch/3_prompt-2.png)

e. Enter a new resolution if you would like to use something different than the default (1366x768). Save the file

![](/img/how-to-install-chrome-remote-desktop-on-arch/4_resolution.png)

f. You should see the confirmation that the setup is complete

![](/img/how-to-install-chrome-remote-desktop-on-arch/5_setup_complete.png)

g. Go to https://remotedesktop.google.com/headless and click on 'Begin'

![](/img/how-to-install-chrome-remote-desktop-on-arch/6_web_setup-1.png)

h. Click on 'Next'

![](/img/how-to-install-chrome-remote-desktop-on-arch/7_web_setup-2.png)

i. Click on 'Authorize'

![](/img/how-to-install-chrome-remote-desktop-on-arch/8_web_setup-3.png)

j. Select the Goole account you would like to use

![](/img/how-to-install-chrome-remote-desktop-on-arch/9_select_account.png)

k. Give it permission

![](/img/how-to-install-chrome-remote-desktop-on-arch/10_select_account-2.png)

l. Click on the copy the button and paste it on your terminal

![](/img/how-to-install-chrome-remote-desktop-on-arch/11_web_setup-4.png)

m. Give the computer a friendly name and a pin to access it

![](/img/how-to-install-chrome-remote-desktop-on-arch/12_paste_terminal.png)

You should get a confirmation that everything went ok

```none
Starting Xvfb on display :20
X server is active.
Launching X session: ['/bin/sh', '/home/victor/.chrome-remote-desktop-session']
Launching host process
['/opt/google/chrome-remote-desktop/chrome-remote-desktop-host', '--host-config=-', '--audio-pipe-name=/home/victor/.config/chrome-remote-desktop/pulseaudio#ae6329c099/fifo_output', '--server-supports-exact-resize', '--ssh-auth-sockname=/tmp/chromoting.victor.ssh_auth_sock', '--signal-parent']
wait() returned (1092272,0)
Host ready to receive connections.
Log file: /tmp/chrome_remote_desktop
```

### Additional Configuration

The additional configuration will allow you to connect to an existing session instead of creating a new one when connecting.

a. Find what display number X is using

```none
$ echo $DISPLAY
:0
```

b. Create a file in `~/.config/chrome-remote-desktop/Xsession` with the display value

```none
$ echo "0" > ~/.config/chrome-remote-desktop/Xsession
```

c. Stop the `chrome-remote-desktop.service`

```none
$ systemctl --user stop chrome-remote-desktop.service
```

d. Check if it stopped with `crd --status`. If it did not, stop it with `crd --stop`

```none
$ crd --status
CRD status: STOPPED
```

e. Take a backup of `/opt/google/chrome-remote-desktop/chrome-remote-desktop`

f. Download the [patched](https://gist.githubusercontent.com/victorbrca/7bd9b351ccf2788a86aa94d296f4d48c/raw/7948453c8da09965b6f23280a1dbf86c432ea4c9/chrome-remote-desktop) `/opt/google/chrome-remote-desktop/chrome-remote-desktop` to the same location, or follow the instructions to manually modify your file [here](https://gist.github.com/nightuser/2ec1b91a66ec33ef0a0a67b6c570eb40#file-use_existing_session-patch).

_**Note:** The patched version was tested with `chrome-remote-desktop 81.0.4044.60-1`_

g. Start the agent with `crd --start` so you can see verbose output. You should receive a confirmation when it starts

```none
$ crd --start

Launching X server and X session.
Using existing Xorg session: 0
Launching host process
['/opt/google/chrome-remote-desktop/chrome-remote-desktop-host', '--host-config=-', '--audio-pipe-name=/home/victor/.config/chrome-remote-desktop/pulseaudio#ae6329c099/fifo_output', '--ssh-auth-sockname=/tmp/chromoting.victor.ssh_auth_sock', '--signal-parent']
Host ready to receive connections.
Log file: /tmp/chrome_remote_desktop_20200402_202207_2vtQSb
```

h. Go to https://remotedesktop.google.com/ from another computer and try to access your computer

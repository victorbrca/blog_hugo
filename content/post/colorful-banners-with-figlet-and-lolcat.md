---
title: "Colorful Banners With figlet and lolcat"
date: 2019-03-10T15:17:22-04:00
draft: false
author: "Victor Mendonça"
description: "Colorinzing your prompt banners with figlet and lolcat"
tags: ["Linux", "Bash"]
---

Banners in *nix like systems is something that is being used for a very long time. System admins would sometimes use it to let users know that the system was going down (nowadays built-in with the `shutdown` command), or setup `motd` messages for SSH logins. Within the past years people got very creative with the use of different fonts and ASCII art.

For today's post we will work With two different apps to display beautiful banners on your systems or config/dot files:

* `figlet` - displays the banners
* `lolcat` - colorizes the banners

### Installation (Arch)

```bash
pacman -Sy figlet lolcat
```

### figlet

Figlet comes with a default font and you can start using it right away

```blank
$ figlet "Hello World"
 _   _      _ _        __        __         _     _
| | | | ___| | | ___   \ \      / /__  _ __| | __| |
| |_| |/ _ \ | |/ _ \   \ \ /\ / / _ \| '__| |/ _` |
|  _  |  __/ | | (_) |   \ V  V / (_) | |  | | (_| |
|_| |_|\___|_|_|\___/     \_/\_/ \___/|_|  |_|\__,_|
```

You can also download additional fonts (which is what we want). This will allow you to create a huge variety of banners.

Head over to the [figlet-fonts](https://github.com/xero/figlet-fonts) project and take a look at the font files (`*.flf`). I would advise downloading the `3d.flf` because that's what we will use here. You can also clone the whole repo.

Place the new font in `~/.local/share/fonts/` and give it as an argument to the `-f` option in figlet:

```
$ figlet -f ~/.local/share/fonts/3d.flf "Hello World"
 ██      ██          ██  ██            ██       ██                  ██      ██
░██     ░██         ░██ ░██           ░██      ░██                 ░██     ░██
░██     ░██  █████  ░██ ░██  ██████   ░██   █  ░██  ██████  ██████ ░██     ░██
░██████████ ██░░░██ ░██ ░██ ██░░░░██  ░██  ███ ░██ ██░░░░██░░██░░█ ░██  ██████
░██░░░░░░██░███████ ░██ ░██░██   ░██  ░██ ██░██░██░██   ░██ ░██ ░  ░██ ██░░░██
░██     ░██░██░░░░  ░██ ░██░██   ░██  ░████ ░░████░██   ░██ ░██    ░██░██  ░██
░██     ░██░░██████ ███ ███░░██████   ░██░   ░░░██░░██████ ░███    ███░░██████
░░      ░░  ░░░░░░ ░░░ ░░░  ░░░░░░    ░░       ░░  ░░░░░░  ░░░    ░░░  ░░░░░░
```

### lolcat

Lolcat produces a rainbow effect on terminal text. For example, try listing a directory and then piping it to `lolcat`. It should produce an output similar to the one below:

![](/img/colorful-banners-with-figlet-and-lolcat/lolcat-list.png)


### Putting it all together

Use the same `figlet` command we used before to print out "Hello World" and pipe it through `lolcat` to get the result below:

![](/img/colorful-banners-with-figlet-and-lolcat/lolbanner.png)

Optionally, you can add Bash alias/function to quickly display colorized banners:

```
lolbanner ()
{
    echo
    figlet -f ~/.local/share/fonts/3d.flf $* | lolcat
    echo
}
```

![](/img/colorful-banners-with-figlet-and-lolcat/vimrc.png)

Have fun adding banners to your dot files, config files and screenshots/videos. Just remember that for config files, the colors will not be saved/shown.

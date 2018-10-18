---
title: "Using Latte as a Dock in Kde"
date: 2018-10-18T12:10:00-04:00
draft: false
author: "Victor Mendonça"
description: "How to configure the Latte as an application launcher dock"
tags: ["KDE", "Linux"]
---

[Latte](https://github.com/psifidotos/Latte-Dock) is a great "newish" dock for KDE that can be used as launching/task dock, or even completely replace your plasma panels. The dock is fast stable with a good "feel", and it fully supports plasma widgets.

I myself do not like the Apple like task docks. I prefer using docks for app launching and let my panels do the task management. I was looking for a fast dock for my Arch VM at work, and Latte was one of the best options. The only thing missing was how to remove the task manager option. But luckily I ended up finding a way on my own (which feels more of a bug, but I won't get into it), and I'm sharing the instructions here.

a. Install `latte-dock`

```bash
$ pacman -Syu latte-dock
```

b. Start the dock (from the terminal or with `Alt+F2`)

c. Right click on the dock and click on "Add/Widgets". Add a widget (like the "Audio Volume" widget)

d. Right click on the dock again and click on "Dock/Panel Settings"

e. Remove the "Latte Widget"

F. Now try to drag an application icon (like from `/usr/share/applications`) to the dock. If that does not work, try opening and closing the "Dock/Panel Settings". It will eventually let you add the application icon (hence why I said it seems like a bug). Once the first application shortcut has been added you can drag more.

Here's a gif animation of the whole process.

![](../img/using-latte-as-a-dock-in-kde/latte-dock.gif)

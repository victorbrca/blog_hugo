---
title: "Alternatives to Top"
date: 2017-08-30T01:34:30-04:00
draft: false
author: "Victor Mendonça"
description: "Looking for alternatives to your usual top command? Here are some options."
tags: ["Linux", "Bash"]
---

Looking for alternatives to your usual top command? Here are some options.

### **htop**

htop is based on ncurses and is compatible with most Linux (and Unix) systems. It's also in most the official repos for most distros.

**Features:**
- Mouse clicks (due to ncurses)
- Defaults to multi-CPU view
- Memory shown in GB

![htop](http://i.imgur.com/IiWRpsJ.png)

**Website:** [http://hisham.hm/htop/](http://hisham.hm/htop/)

### **vtop**

vtop takes more of a graphical approach to top, while concentrating more on simplicity. It displays CPU and Memory usage live charts, as well as a running process list. It runs on Node.js and can be easily installed with `npm install -g vtop`.

**Features:**
- Mouse clicks
- Themes
- Live chart (CPU, memory)
- Process list

![vtop](http://i.imgur.com/TT4J6as.png)

**Website:** [https://github.com/MrRio/vtop](https://github.com/MrRio/vtop)

### **gtop**

gtop takes the graphical interface of vtop to another level. Just like vtop, it also displays a live chart of both CPU and memory, however gtop adds a network live chart to the list, and pie charts for both memory and swap, as well as storage usage.

gtop also runs on Node.js, and can easily be installed with `npm install gtop -g`.

**Features:**
- Live chart (CPU, memory/swap, network)
- Pie chart (memory, swap, disk usage)
- Process list

![gtop](http://i.imgur.com/brogwgg.png)

**Website:** [https://github.com/aksakalli/gtop](https://github.com/aksakalli/gtop)

### **s-tui**

While s-tui is not a process monitoring utility, it displays great information about your process status. It's python based and uses little resource. You can easily install it with `sudo pip install s-tui`.

**Features:**
- Processor info
- Live charts (CPU freq, utilization, temperature and power usage)
- Built-in CPU stress testing

![](https://github.com/amanusk/s-tui/blob/master/ScreenShots/s-tui.gif?raw=true)

**Website:** [https://amanusk.github.io/s-tui/](https://amanusk.github.io/s-tui/)

---
title: "Top Skills That Will Make You Stand Out as a Linux Admin: Part 1"
date: 2025-08-22T11:54:29-04:00
draft: false
author: "Victor Mendonça"
description: "Learn essential Bash shortcuts and history tricks to work faster and stand out as a Linux admin."
tags: ["Bash", "Linux"]
---

Working as a Linux administrator often means being pulled in many directions and juggling a variety of technologies. That doesn’t leave much time to revisit the basics. Over time, work becomes repetitive, and we end up doing things the same way we learned years ago—without stopping to refine or discover smarter approaches.

With this series of posts, I hope to share tips and shortcuts that will help you work faster and smarter—and maybe even stand out among your coworkers by pulling off a few neat tricks.

Try these out at least once—and if you’re still stuck going to the office, print them out and stick them on your cubicle wall.

Today, we’re going to focus on Bash navigation and history.

![picture 0](/img/top-skills-that-will-make-you-stand-out-as-a-linux-admin/TuxandBashGuideinFocus.png)

### Tab Completion

We all know that `[TAB]` can be used for both path and command completion, but I’m always surprised by how many admins who aren’t fully comfortable with the shell forget to use it. You’re not going to break anything or use up some imaginary “tab credits.” So use the hell out of tab completion—keep hitting that key until you land on what you need.

And here’s a bonus: don’t forget that `[SHIFT]+[TAB]` takes you back. So if you scroll past the option or path you were aiming for, just reverse it. Simple, fast, and much smoother than retyping.

### Command Navigation

The commands below will help you navigate more efficiently and productively in a shell:

+ `Ctrl+a` - Move to the beginning of the line (same as `[HOME]`)
+ `Ctrl+e` - Move to the end of the line (same as `[END]`)
+ `Ctrl+left/right` - Move backward/forward one word
+ `Ctrl+w` - Delete the previous word
+ `Alt+t` - Swap the current word with the previous one
+ `Esc+v` - Edit the current line in an external editor (this is the `edit-and-execute-command` function). It uses the default editor set in `$VISUAL` or `$EDITOR`, which you can change if needed

![picture 0](/img/top-skills-that-will-make-you-stand-out-as-a-linux-admin/command-navigation.gif)

_Note: I remapped my `edit-and-execute-command` shortcut to `Esc+i`, which feels more comfortable for me._

## Path Navigation

Navigating between directories is something sysadmins do constantly. Being able to move around quickly—and knowing a few tricks—can speed up your work exponentially. It can even impress your coworkers when they see how fast you glide through the shell.

+ **Tab completion** — We already covered this, but it’s worth repeating: use it and abuse it!
+ `cd` — Jumps straight back to your home folder. It sounds obvious, but many people forget this one.
+ `cd -` — Switches to the previous directory you were in. Great for bouncing back and forth.
+ `cd ~` — Navigates to your home folder. The tilde (`~`) is very handy for quickly moving to subdirectories inside your home directory, e.g.: `cd ~/Downloads`

![picture 0](/img/top-skills-that-will-make-you-stand-out-as-a-linux-admin/Penguin-and-Staples-on-Office-Desk.png)

### History

As you probably already know, `history` lets you go back to previous commands and re-execute them. Mastering how to quickly search and re-use previous commands saves you from tediously retyping the same things over and over.

+ `Ctrl+r` — Search your history for a specific string
  + Use `[Shift]` to go back if you skip past the command you wanted
  + Start typing the string before pressing `Ctrl+r` to act as a filter and only see matches for that string
+ `!!` — Re-execute the last command. Super handy when you forget sudo. Just type `sudo !!`
+ `!$` — The last argument of your previous command. Useful if you mistyped a command and want to reuse its argument, e.g.:

    ```bash
    userdle testuser   # typo
    userdel !$         # fixes it using the last argument
    ```

  + It’s also great for running the last command with sudo, similar to `!!`
+ `Alt+.` — Inserts the last argument of your previous command directly into the current line (and shows it to you). It’s effectively the same as typing `!$`, but more interactive since it shows the result as you type. Keep pressing it to cycle through arguments from earlier commands.

**💡 Tips:**

+ Remember to use `Esc+v` to quickly open a previous command in your editor if it needs modification
+ Bash history can be customized to be even more powerful:
  + `HISTTIMEFORMAT` — Add timestamps to your command history. E.g.:

        ```
        $ history | tail -2
        1029  +2025/08/22 14:52:19 uptime
        1030  +2025/08/22 14:52:21 history | tail -2
        ```

  + `HISTSIZE` and `HISTFILESIZE` — Increase how many commands are stored in memory and on disk
  + `shopt -s histappend` — Append to your history file instead of overwriting it. This way you don’t lose commands when multiple shells are open

## Conclusion

That wraps up our quick dive into Bash navigation, history, and some handy shortcuts to speed up your workflow. Try them out, and stay tuned—there’s plenty more cool stuff coming in the next posts!

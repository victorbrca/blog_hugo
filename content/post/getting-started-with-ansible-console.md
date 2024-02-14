---
title: "Getting Started with Ansible Console"
date: 2024-02-14T11:29:52-03:00
draft: false
author: "Victor Mendonça"
description: "A quick introduction to Ansible Console"
tags: ["Linux", "Ansible"]
---

Are you ready to level up your Linux game and wield the power of automation like a true DevOps ninja? Enter Ansible Console, the interactive command-line interface that puts the force of Ansible at your fingertips!

In this introduction post, I'll take you on tour of Ansible Console, teaching you how to run ad-hoc tasks against your inventory with ease. 

### What is Ansible Console

Ansible Console is an interactive console for executing ad-hoc tasks against hosts defined in your Ansible inventory. It offers built-in command completion and a user-friendly interface, making it a versatile tool for system administrators and automation engineers.

## Getting Started

To begin using Ansible Console, launch it by invoking `ansible-console` with the default inventory, or by specifying a custom inventory file. 

Using the default inventory:

```none
$ ansible-console
```

Specifying an inventory file:

```none
$ ansible-console -i [inventory]
```

Once launched, the console prompt will display essential information about the current context, including group names, the number of hosts, and forks.

```none
ansible@all (6)[f:10]$
		  |  |    |- forks
		  |  |- number of hosts
		  |- group
```

Keep an eye on the prompt color – if it turns red, it's a subtle reminder that the `become` flag is set to true. Safety first, folks!

## Inventory Management

Managing your inventory has never been easier. With Ansible Console, you can list hosts, groups, and even test connections with a flick of your wrist (or a tap of your keyboard).

Need to list all your hosts? No problem, just use the `list` sub-command:

```none
ansible@all (6)[f:10]$ list
nut-pi
ubuntu-pi4
ubuntu-backup
ubuntu-lenovomini
freenas
pfsense
```

Want to flex your group management skills? Try this:

```none
ansible@all (6)[f:10]$ list groups
all
centos
freebsd
linux
pfsense
raspberrypi
raspbian
rhel
truenas
ubuntu
ungrouped
```

And if you're feeling a bit adventurous, why not test the connection to your hosts using the trusty `ping` or the `ls` command?

`ls`

```bash
ansible@all (6)[f:10]$ ls
freenas | CHANGED | rc=0 >>

pfsense | FAILED! => {
		"changed": false,
		"module_stderr": "Shared connection to 192.168.1.1 closed.\r\n",
		"module_stdout": "/bin/sh: /usr/local/bin/python: not found\r\n",
		"msg": "The module failed to execute correctly, you probably need to set the interpreter.\nSee stdout/stderr for the exact error",
		"rc": 127
}
ubuntu-lenovomini | CHANGED | rc=0 >>

ubuntu-backup | CHANGED | rc=0 >>

ubuntu-pi4 | CHANGED | rc=0 >>

nut-pi | CHANGED | rc=0 >>
```

`ping`

```bash
ansible@all (6)[f:10]$ ping
freenas | SUCCESS => {
		"changed": false,
		"ping": "pong"
}
pfsense | FAILED! => {
		"changed": false,
		"module_stderr": "Shared connection to 192.168.1.1 closed.\r\n",
		"module_stdout": "/bin/sh: /usr/local/bin/python: not found\r\n",
		"msg": "The module failed to execute correctly, you probably need to set the interpreter.\nSee stdout/stderr for the exact error",
		"rc": 127
}
ubuntu-lenovomini | SUCCESS => {
		"changed": false,
		"ping": "pong"
}
ubuntu-backup | SUCCESS => {
		"changed": false,
		"ping": "pong"
}
ubuntu-pi4 | SUCCESS => {
		"changed": false,
		"ping": "pong"
}
nut-pi | SUCCESS => {
		"changed": false,
		"ping": "pong"
}
```

### Selecting Host or Group

You can select a host or a group with the 'cd' command (note how the prompt changes).

*Host*

```none
ansible@all (6)[f:10]$ cd pfsense
ansible@pfsense (1)[f:10]$
```
	
*Group*

```none
ansible@all (6)[f:10]$ cd ubuntu
ansible@ubuntu (3)[f:10]$
```

_You can also use host patterns eg.: `app*.dc*:!app01*`_

### Configuration Customization

Ansible Console puts the power in your hands with customizable configuration options. Whether you need to specify the remote user, adjust the number of forks, toggle the `become` flag (and many more), Ansible Console has got you covered.

```none
ansible@all (0)[f:5]$ remote_user [username]
ansible@all (0)[f:5]$ forks 7
ansible@all (0)[f:5]$ become [true|false]
ansible@all (0)[f:5]$ check [true|false]
ansible@all (0)[f:5]$ diff [true|false]
```

### Modules

Ah, modules – the bread and butter of Ansible automation. With Ansible Console, you can unleash the full potential of Ansible modules right from your command line. 

Use the `help` sub-command to get a list of available modules:

```none
ansible@ubuntu (3)[f:10]$ help
	
Documented commands (type help <topic>):
========================================

[... truncated ...]

wti.remote.cpm_syslog_server_info
wti.remote.cpm_temp_info
wti.remote.cpm_time_config
wti.remote.cpm_time_info
wti.remote.cpm_user

Undocumented commands:
======================
ping
```

Explore the vast array of available modules or dive deep into specific modules for detailed information and usage examples. Simply provide the module name as an argument to `help`. And don't forget that Ansible Console has built-in tab completion, so make sure to use and abuse it:

```none
ansible@ubuntu (3)[f:10]$ help copy

Copy files to remote locations
Parameters:
	src Local path to a file to copy to the remote server.
	content When used instead of O(src), sets the contents of a file directly to the specified value.
	dest Remote absolute path where the file should be copied to.
	backup Create a backup file including the timestamp information so you can get the original file back if you somehow clobbered it incorrectly.
	force Influence whether the remote file must always be replaced.
	mode The permissions of the destination file or directory.
	directory_mode Set the access permissions of newly created directories to the given mode. Permissions on existing directories do not change.
	remote_src Influence whether O(src) needs to be transferred or already is present remotely.
	follow This flag indicates that filesystem links in the destination, if they exist, should be followed.
	local_follow This flag indicates that filesystem links in the source tree, if they exist, should be followed.
	checksum SHA1 checksum of the file being transferred.
	decrypt This option controls the autodecryption of source files using vault.
	owner Name of the user that should own the filesystem object, as would be fed to I(chown).
	group Name of the group that should own the filesystem object, as would be fed to I(chown).
	seuser The user part of the SELinux filesystem object context.
	serole The role part of the SELinux filesystem object context.
	setype The type part of the SELinux filesystem object context.
	selevel The level part of the SELinux filesystem object context.
	unsafe_writes Influence when to use atomic operation to prevent data corruption or inconsistent reads from the target filesystem object.
	attributes The attributes the resulting filesystem object should have.
	validate The validation command to run before copying the updated file into the final destination.
```

#### Module Usage Examples

##### Installing a Package

```none
ansible@ubuntu (3)[f:10]$ ansible.builtin.package name=git
```

##### Installing a PIP Package

```none
 ansible@ubuntu (3)[f:10]$ ansible.builtin.pip name=youtube-dl
```

#### Modules vs Commands

Anything that you type and it's not a module will be run as a command. For example:

```bash
ansible@ubuntu (3)[f:10]$ cat /etc/hostname
ubuntu-lenovomini | CHANGED | rc=0 >>
ubuntu-lenovomini
ubuntu-pi4 | CHANGED | rc=0 >>
ubuntu-pi4
ubuntu-backup | CHANGED | rc=0 >>
ubuntu-backup
```

However, if a module exists, `ansible-console` will invoke that module, or give you an error.

Example with `hostname`, which is a built-in module:

```bash
ansible@ubuntu (3)[f:10]$ help hostname
Manage hostname
Parameters:
	name Name of the host.
	use Which strategy to use to update the hostname.
```

The module expects a parameter, so it fails:

```bash
ansible@ubuntu (3)[f:10]$ hostname
ubuntu-lenovomini | FAILED! => {
		"changed": false,
		"msg": "missing required arguments: name"
}
ubuntu-pi4 | FAILED! => {
		"changed": false,
		"msg": "missing required arguments: name"
}
ubuntu-backup | FAILED! => {
		"changed": false,
		"msg": "missing required arguments: name"
}
```

Another example, but now with `yum`:

```none
ansible@ubuntu (3)[f:10]$ yum check-update -q
 [ERROR]: Unable to build command: this task 'yum' has extra params, which is only allowed in the following modules: ansible.legacy.group_by, shell, ansible.legacy.set_fact, ansible.builtin.include_tasks, command, ansible.legacy.script, ansible.legacy.include,
ansible.builtin.group_by, ansible.legacy.shell, set_fact, ansible.builtin.shell, win_command, ansible.legacy.command, ansible.legacy.win_shell, ansible.builtin.set_fact, ansible.builtin.add_host, ansible.legacy.import_role, include_role, ansible.builtin.win_shell, include,
ansible.legacy.win_command, ansible.legacy.include_tasks, include_tasks, ansible.legacy.add_host, ansible.builtin.include_role, group_by, ansible.builtin.meta, ansible.builtin.raw, import_role, ansible.legacy.raw, ansible.builtin.command, ansible.legacy.include_vars, script,
win_shell, ansible.builtin.script, ansible.legacy.import_tasks, add_host, meta, ansible.builtin.import_tasks, ansible.windows.win_shell, ansible.builtin.include, ansible.windows.win_command, ansible.builtin.include_vars, raw, ansible.builtin.import_role, include_vars, import_tasks,
ansible.builtin.win_command, ansible.legacy.meta, ansible.legacy.include_role
```

But sometimes you may need to run a command that exists as a module instead of the module itself. This can easily be done with the `!`:

```none
ansible@ubuntu-pi4 (1)[f:10]$ !apt list --upgradable
ubuntu-pi4 | CHANGED | rc=0 >>
Listing...
base-files/focal-updates 11ubuntu5.8 arm64 [upgradable from: 11ubuntu5.7]
gnome-shell-common/focal-updates 3.36.9-0ubuntu0.20.04.3 all [upgradable from: 3.36.9-0ubuntu0.20.04.2]
gnome-shell/focal-updates 3.36.9-0ubuntu0.20.04.3 arm64 [upgradable from: 3.36.9-0ubuntu0.20.04.2]
libnss-systemd/focal-updates 245.4-4ubuntu3.23 arm64 [upgradable from: 245.4-4ubuntu3.22]
libpam-systemd/focal-updates 245.4-4ubuntu3.23 arm64 [upgradable from: 245.4-4ubuntu3.22]
libsystemd0/focal-updates 245.4-4ubuntu3.23 arm64 [upgradable from: 245.4-4ubuntu3.22]
libudev1/focal-updates 245.4-4ubuntu3.23 arm64 [upgradable from: 245.4-4ubuntu3.22]
motd-news-config/focal-updates 11ubuntu5.8 all [upgradable from: 11ubuntu5.7]
systemd-sysv/focal-updates 245.4-4ubuntu3.23 arm64 [upgradable from: 245.4-4ubuntu3.22]
systemd-timesyncd/focal-updates 245.4-4ubuntu3.23 arm64 [upgradable from: 245.4-4ubuntu3.22]
systemd/focal-updates 245.4-4ubuntu3.23 arm64 [upgradable from: 245.4-4ubuntu3.22]
tailscale/unknown 1.58.2 arm64 [upgradable from: 1.56.1]
tzdata/focal-updates 2023d-0ubuntu0.20.04 all [upgradable from: 2023c-0ubuntu0.20.04.2]
udev/focal-updates 245.4-4ubuntu3.23 arm64 [upgradable from: 245.4-4ubuntu3.22]
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
```

### Variables

Collection variables are not available by default because `ansible-console`, unlike `ansible-playbook`, doesn't gather facts before running:

```bash
ansible@ubuntu-pi4 (1)[f:10]$ debug msg="{{ ansible_facts.architecture }}"
ubuntu-pi4 | FAILED! => {
		"msg": "The task includes an option with an undefined variable. The error was: 'dict object' has no attribute 'architecture'. 'dict object' has no attribute 'architecture'. 'dict object' has no attribute 'architecture'. 'dict object' has no attribute 'architecture'"
}
```

But fear not, you can manually invoke the `setup` module and either filter the values, or print them with the `debug` module:

```bash
ansible@ubuntu-pi4 (1)[f:10]$ setup filter="*architecture"
ubuntu-pi4 | SUCCESS => {
		"ansible_facts": {
				"ansible_architecture": "aarch64"
		},
		"changed": false
}

ansible@ubuntu-pi4 (1)[f:10]$ debug msg="{{ ansible_facts.architecture }}"
ubuntu-pi4 | SUCCESS => {
		"msg": "aarch64"
}
```

### Conclusion

With its intuitive interface and powerful functionality, Ansible Console offers a convenient way to execute ad-hoc tasks and manage your Ansible inventory interactively. By mastering Ansible Console, you can streamline your automation workflows and improve operational efficiency in your environment.
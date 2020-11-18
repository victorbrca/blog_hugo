---
title: "RHCSA v8: Boot Targets, Systemd Targets and root Password Reset"
date: 2020-11-14T16:00:00-05:00
draft: false
author: "Victor Mendonça"
description: "The ultimate guide to boot/systemd targets and password reset"
tags: ["Linux", "RedHat", "RHCSA", "Systemd"]
---

For quite a while the RHCSA exam has covered topics related to boot targets and the famous root password reset. However, with the introduction of systemd and other related changes this can sometimes become a bit confusing. The methods that were once used for RHEL 6/7 may no longer be available or may not be the 'official' way of doing things anymore.

I will try to clear some of the differences so we can have a better understanding of what's needed for the RHCSAv8 exam.

#### Exam Topics we will cover
[Red Hat Certified System Administrator (RHCSA) exam objectives](https://www.redhat.com/en/services/training/ex200-red-hat-certified-system-administrator-rhcsa-exam?section=Objectives)

**Operate running systems**

+ Boot systems into different targets manually
+ Interrupt the boot process in order to gain access to a system

Boot Targets
---

On this section I will cover topics related to the '_Boot systems into different targets manually_' objective.

### Olders SysV

SysV is an older startup mechanism that was used by many Unix-like operating systems. It was replaced by Systemd and it's no longer used by Red Hat. However, you will sill find commands that references SysV and it's run levels.

_From Red Hat Enterprise Linux 3: Reference Guide_

> The SysV init runlevel system provides a standard process for controlling which programs init launches or halts when initializing a runlevel. SysV init was chosen because it is easier to use and more flexible than the traditional BSD-style init process.

The following runlevels are defined by default for Red Hat Enterprise Linux:
```none
0 — Halt
1 — Single-user text mode
2 — Not used (user-definable)
3 — Full multi-user text mode
4 — Not used (user-definable)
5 — Full multi-user graphical mode (with an X-based login screen)
6 — Reboot
```

##### SysV Commands

You can find what runlevel the system currently is by executing the command `runlevel`

```none
# runlevel
N 5
```

And you can change the system runlevel by running `telinit` with a runlevel number (0-6)

```none
# telini 6
```

**📝 NOTE:** _You should not be using SysV commands to change the runlevels. You should use systemd commands instead._

### Systemd Targets

[RHEL 8 > Configuring basic system settings > Chapter 3. Managing services with systemd > 3.3. Working with systemd targets](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/managing-services-with-systemd_configuring-basic-system-settings#working-with-systemd-targets_managing-services-with-systemd)

Systemd targets are similar to SysV runlevels, and they are what you should use/reference from now on (including for the RHCSAv8 exam).

_From Arch Wiki_

> systemd uses targets to group units together via dependencies and as standardized synchronization points. They serve a similar purpose as runlevels but act a little differently. Each target is named instead of numbered and is intended to serve a specific purpose with the possibility of having multiple ones active at the same time.


#### Runlevels and Systemd

While runlevels are not used anymore, they are still referenced (by older codes and such). Systemd provides a compatibility layer to it's targets.

_From `runlevel` man page_

> "Runlevels" are an obsolete way to start and stop groups of services used in SysV init. systemd
>  provides a compatibility layer that maps runlevels to targets, and associated binaries like
>  runlevel. Nevertheless, only one runlevel can be "active" at a given time, while systemd can
>  activate multiple targets concurrently, so the mapping to runlevels is confusing and only
>  approximate. Runlevels should not be used in new code, and are mostly useful as a shorthand way
>  to refer the matching systemd targets in kernel boot parameters.

![](/img/rhcsa8-boot-targets-system-targets-and-root-password-reset/runlevel-table.png)


#### Working with Systemd Targets

Let's go through the commands that will allow us to list, view and change the Systemd targets.

**Listing the available targets**

```none
# systemctl list-units --type=target

UNIT                   LOAD   ACTIVE SUB    DESCRIPTION                 
basic.target           loaded active active Basic System                
cryptsetup.target      loaded active active Local Encrypted Volumes     
getty.target           loaded active active Login Prompts               
graphical.target       loaded active active Graphical Interface         
local-fs-pre.target    loaded active active Local File Systems (Pre)    
local-fs.target        loaded active active Local File Systems          
multi-user.target      loaded active active Multi-User System           
network-online.target  loaded active active Network is Online           
network-pre.target     loaded active active Network (Pre)               
network.target         loaded active active Network                     
nfs-client.target      loaded active active NFS client services         
nss-user-lookup.target loaded active active User and Group Name Lookups
paths.target           loaded active active Paths                       
remote-fs-pre.target   loaded active active Remote File Systems (Pre)   
remote-fs.target       loaded active active Remote File Systems         
rpc_pipefs.target      loaded active active rpc_pipefs.target           
rpcbind.target         loaded active active RPC Port Mapper             
slices.target          loaded active active Slices                      
sockets.target         loaded active active Sockets                     
sound.target           loaded active active Sound Card                  
sshd-keygen.target     loaded active active sshd-keygen.target          
swap.target            loaded active active Swap                        
sysinit.target         loaded active active System Initialization       
timers.target          loaded active active Timers  
```

**Get the current target**

```none
# systemctl get-default
graphical.target
```

**Set the Systemd target for next boot**

```none
# systemctl set-default [target]
```

**Change the Systemd target without reboot**

```none
# systemctl isolate [target]
```

**Change into rescue/emergency mode**

_**Note:** This is related to the next session where we will cover rescue modes._

```none
# systemctl isolate [rescue|emergency]

or

# systemctl [rescue|emergency]
```

Rescue Modes
---
_This section is related to the 'Interrupt the boot process in order to gain access to a system' objective._

Rescue and emergency modes allow you to boot the OS with very few services running and perform administrative tasks to attempt to resolve/repair/recover the system.

There are different types of rescue and emergency modes:

+ Legacy
  + Boot Parameters:
      - `rescue`
      - `emergency`
+ Systemd
  + Boot Parameters:
      - `systemd.unit=rescue.target`
      - `systemd.unit=emergency.target`
+ Installation program's (Anaconda) rescue mode
  + Requires install media
  + Boot Parameters:
      - `inst.rescue`

### rescue

[RHEL 8 > Configuring basic system settings > Chapter 3. Managing services with systemd > Chapter 3. Managing services with systemd > 3.3. Working with systemd targets > 3.3.5. Changing to rescue mode](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/managing-services-with-systemd_configuring-basic-system-settings#sect-Managing_Services_with_systemd-Targets-Rescue)

Rescue mode provides a convenient single-user environment and allows you to repair your system in situations when it is unable to complete a regular booting process. In rescue mode, the system attempts to mount all local file systems and start some important system services, but it does not activate network interfaces or allow more users to be logged into the system at the same time.  

_**Note:** Equivalent to the old single user mode, where some services are started and every disk is mounted._

**Boot Parameter:**

+ Legacy: `rescue`
+ Systemd: `systemd.unit=rescue.target`

**Summary:**

+ Requires root password to enter this mode
+ Mounts all local filesystems (RW)
+ No network
+ Starts important services  
+ Single-user mode

#### Instructions

a. At boot, hit `e` to edit the boot parameters

b. Add one of the boot parameters at the end of the line that starts with linux

c. Press 'Ctrl + x' to start

### emergency

[RHEL 8 > Configuring basic system settings > Chapter 3. Managing services with systemd > Chapter 3. Managing services with systemd > 3.3. Working with systemd targets > 3.3.6. Changing to emergency mode](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/managing-services-with-systemd_configuring-basic-system-settings#sect-Managing_Services_with_systemd-Targets-Emergency)

Emergency mode provides the most minimal environment possible and allows you to repair your system even in situations when the system is unable to enter rescue mode. In emergency mode, the system mounts the root file system in read only mode, does not attempt to mount any other local file systems, does not activate network interfaces, and only starts a few essential services.

**Boot Parameter:**

+ Legacy: `emergency`
+ Systemd: `systemd.unit=emergency.target`

**Summary:**

+ Requires root password to enter this mode
+ Mounts the root filesystem only (RO)
+ No network
+ Only essential services are started
+ The system does not load any init scripts
+ Multi-user mode

#### Instructions

a. At boot, hit `e` to edit the boot parameters

b. Add one of the boot parameters at the end of the line that starts with linux

c. Press 'Ctrl + x' to start

### rd.break

Breaks to an interactive shell while in the 'initrd' allowing interaction before the system disk is mounted. The main '/' is available under '/sysroot'. Useful if you forgot root's password.

**Boot Parameter:**

+ `rd.break`

#### Instructions

a. At boot, hit `e` to edit the boot parameters

b. Add `rd.break` option at the end of the line that starts with linux

c. Press 'Ctrl + x' to start

### Anaconda rescue

[RHEL 8 > Performing a standard RHEL installation > Appendix A. Troubleshooting > A.3.8. Using rescue mode](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/performing_a_standard_rhel_installation/installer-troubleshooting_installing-rhel#using-rescue-mode_troubleshooting-after-installation)

The installation program’s rescue mode is a minimal Linux environment that can be booted from the Red Hat Enterprise Linux DVD or other boot media. It contains command-line utilities for repairing a wide variety of issues. Rescue mode can be accessed from the Troubleshooting menu of the boot menu. In this mode, you can mount file systems as read-only, blacklist or add a driver provided on a driver disc, install or upgrade system packages, or manage partitions.

#### Instructions

a. Boot the system from either minimal boot media, or a full installation DVD or USB drive, and wait for the boot menu to be displayed.  

b. From the boot menu, either select Troubleshooting > Rescue a Red Hat Enterprise Linux system option, or append the `inst.rescue` option to the boot command line. To enter the boot command line, press the Tab key on BIOS-based systems or the `e` key on UEFI-based systems.   

Password Reset
---
[RHEL 8 > Configuring basic system settings > Chapter 9. Changing and resetting the root password > 9.3. Resetting the forgotten root password on boot](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/changing-and-resetting-the-root-password-from-the-command-line_configuring-basic-system-settings#resetting-the-forgotten-root-password-on-boot_changing-and-resetting-the-root-password-from-the-command-line)

I will now cover different methods on how to change the root password if you forgot it. This is a common question for the RHCSA exams (or so I've heard as I have yet to take mine).

### Method 1: Fixing SELinux context before reboot

This is not the "official" method, but it's the one I like the most. It saves a bit of time from having to relabel the whole system for SELinux context.

a. Boot into `rd.break` mode

b. Re-mount sysroot in RW

```none
switch_root:/# mount -o rw,remount /sysroot
```

c. Chroot into sysroot

```none
switch_root:/# chroot /sysroot
```

d. Change the password for root

```none
sh-4.4# passwd
```

e. Load the SELinux policies

```none
sh-4.4# load_policy -i
```

f. Fix the policy for `/etc/shadow`

```none
sh-4.4# restorecon -v /etc/shadow
```

g. Exit chroot

```none
sh-4.4# exit
```

h. Remount as RO

```none
switch_root:/# mount -o ro,remount /sysroot
```

i. Reboot

```none
switch_root:/# reboot
```

### Method 2: Setting autorelabel on reboot

This is the most common method that you will find online for the RHCSA exam. It's also the method shown in Red Hat's [official documentation for RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/changing-and-resetting-the-root-password-from-the-command-line_configuring-basic-system-settings#resetting-the-forgotten-root-password-on-boot_changing-and-resetting-the-root-password-from-the-command-line).

a. Boot into `rd.break` mode

b. Re-mount sysroot in RW

```none
switch_root:/# mount -o rw,remount /sysroot
```

c. Chroot into sysroot

```none
switch_root:/# chroot /sysroot
```

d. Change the password for root

```none
sh-4.4# passwd
```

e. Force the relabel of SELinux context for the filesystem

```none
sh-4.4# touch /.autorelabel
```

f. Exit chroot

```none
sh-4.4# exit
```

g. Remount as RO

```none
switch_root:/# mount -o ro,remount /sysroot
```

h. Reboot

```none
switch_root:/# reboot
```

_**Note:** It takes a while for the system to relabel all the files with SELinux context._


### Method 3: Setting SELinux to permissive and manually fixing the context
_**Links:**_ [Red Hat Learning Community](https://learn.redhat.com/t5/Platform-Linux/Crack-root-password/td-p/4161/page/2) - [Fedora Documentation](https://docs.fedoraproject.org/en-US/Fedora/26/html/System_Administrators_Guide/sec-Changing_and_Resetting_the_Root_Password.html)

With this method we temporarily set SELinux to permissive mode, make our password changes, reload the SELinux context for the shadow file, and then re-enable SELinux.

**⚠️ WARNING:** _While this initially worked for me on RHEL 8 (you will get a lot of SELinux errors, which is expected), after a while my VM would hang and I could no longer login as any user or restart it._

a. Boot into `rd.break` mode by adding `rd.break enforcing=0` to the boot line

b. Re-mount sysroot in RW

```none
switch_root:/# mount -o rw,remount /sysroot
```

c. Chroot into sysroot

```none
switch_root:/# chroot /sysroot
```

d. Change the password for root

```none
sh-4.4# passwd
```

e. Exit chroot

```none
sh-4.4# exit
```

f. Remount as RO

```none
switch_root:/# mount -o ro,remount /sysroot
```

g. Exit switch_root and wait for the system to finish booting

```none
switch_root:/# exit
```

h. As root, fix SELinux context for `/etc/shadow`

```none
[root@localhost ~]# restorecon -v /etc/shadow
```

i. Set SELinux to enforcing mode

```none
[root@localhost ~]# setenforce 1
```

### Method 4: Disabling SELinux and manually fixing the context

This is very similar to method 3, however we are completely disabling SELinux.

**⚠️ WARNING:** _This method did not work for me. My VM did not finish booting after making the changes._

a. Boot into `rd.break` mode by adding `rd.break selinux=0` to the boot line

b. Re-mount sysroot in RW

```none
switch_root:/# mount -o rw,remount /sysroot
```

c. Chroot into sysroot

```none
switch_root:/# chroot /sysroot
```

d. Change the password for root

```none
sh-4.4# passwd
```

e. Exit chroot

```none
sh-4.4# exit
```

f. Remount as RO

```none
switch_root:/# mount -o ro,remount /sysroot
```

g. Exit switch_root and wait for the system to finish booting

```none
switch_root:/# exit
```

h. As root, fix SELinux context for `/etc/shadow`

```none
[root@localhost ~]# restorecon -v /etc/shadow
```

i. Re-enable SELinux

```none
[root@localhost ~]# setenforce 1
```

- - -

Conclusion
---

I hope I helped you make a bit more sense from all of the different options and info around this topic. If you have any comments, corrections or addition, please leave them below. I would love to hear from you.

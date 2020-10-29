---
title: "RHCSA v8 - What's New"
date: 2020-03-14T11:31:56-04:00
draft: false
author: "Victor Mendonça"
description: "What's new in the RHCSA RHEL 8 exam"
tags: ["Linux", "RedHat", "RHCSA"]
---

The updated RHCSA exam for RHEL 8 has been out since last year, however, the old exam is still available, and will stay available until August 1st, 2020:

- - -
![](/img/rhcsa---whats-new/deadline.png)
- - -

You may be trying to figure out what version will be best for you, and I'm hoping the information here will help you.

Something to take in consideration is that the exam is valid for 3 years, or 2 releases (meaning that once RHEL 9 is out, your RHSCA on RHEL 7 is no longer valid). And seeing on how RHEL 6 to 7 took 4 years, and RHEL 7 to 8 took 5 years, with RHCSA v7 you will most likely run out of the 3 years before RHEL 9 is released.

### Objective Differences - RHCSA v7 vs v8

#### => RHCSA v7 - Content No Longer Needed for V8

**Operate running systems**

+ Access a virtual machine’s console.
+ Start and stop virtual machines.

**Create and configure file systems**

+ Mount and unmount CIFS and NFS network file systems.
  + CIFS is no longer covered
+ Create and manage Access Control Lists (ACLs).
  + Looks like this is still in the exam, but under "Manage Security => Create and use file access control lists"

**Deploy, configure, and maintain systems**

+ Install Red Hat Enterprise Linux systems as virtual guests.
+ Configure systems to launch virtual machines at boot.
+ Configure a system to use time services.
+ Update the kernel package appropriately to ensure a bootable system.

**Manage users and groups**

+ Configure a system to use an existing authentication service for user and group information.

**Manage security**

+ Configure firewall settings using firewall-config, firewall-cmd, or iptables.
  + iptables is no longer covered


#### => RHCSA v8 - New Content

**Operate running systems**

+ Preserve system journals

**Create and configure file systems**

+ Configure disk compression
+ Manage layered storage

**Deploy, configure, and maintain systems**

+ Configure time service clients
  + Red Hat doesn’t ask candidates to set up time service client and server anymore but only time service clients. Knowledge of the NTP daemon is no longer necessary, that of Chrony is enough.
+ Work with package module streams

**Manage basic networking**

+ Configure IPv4 and IPv6 addresses

**Manage users and groups**

+ Configure superuser access

- - -

**References:**

+ https://www.redhat.com/en/services/training/ex200-red-hat-certified-system-administrator-rhcsa-exam
+ https://www.certdepot.net/rhel7-rhcsa-exam-objectives/
+ https://www.certdepot.net/rhel8-rhcsa-whats-new/

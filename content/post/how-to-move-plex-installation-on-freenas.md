---
title: "How to Move Plex Installation on FreeNAS 11.3"
date: 2020-03-30T13:56:51-04:00
draft: false
author: "Victor Mendonça"
description: "How to Move Plex data on FreeNAS 11.3 "
tags: ["FreeNAS"]
---

I had some free time this weekend and decided to upgrade my FreeNAS. I went from 11.1 to 11.3-UI and the upgrade installed without any issues. However, after the reboot I discovered that my jails and plugins were missing from the UI and that they were not running. I had read the manual ([FreeNAS® 11.3-U1 User Guide](https://www.ixsystems.com/documentation/freenas/11.3-U1/freenas.html)) before the upgrade and and the instructions did not mention anything about the plugins, so I was little worried.

After spending a lot of time researching I discovered that on FreeNAS 11.2 the project started to use the 'iocage' jail method instead of 'warden'. FreeNAS 11.2 had the option of migrating your jails, and it could even display then from the UI. But for 11.3-UI that was no longer an option.

If you are on the same boat as me, the instructions below will help you quickly re-create a new Plex jail a move your old data to the new jail. If you have not upgraded to 11.3-UI you might want to convert your jail before upgrading. There are a lot of tutorials on-line on how to convert your jail that might be more useful to you.

### Instructions

a. Create the `plex` user with UID `972` (this is the username and UID that is used by the project)

b. If desired, create a new Dataset to have Plex data outside of the plugin Dataset. I won't go into details for this type of setup here as I keep my Plex data inside the Plex plugin Dataset

c. Install the 'Plex Media Server' plugin ([official instructions](https://support.plex.tv/articles/installing-plex-media-server-on-freenas/))

d. Stop the plugin

e. Go to 'Storage => Pools' and edit the ACL for the Dataset where your media is saved. We want to give access to the 'plex' user (in case the files are not owned by 'plex')

![](/img/how-to-move-plex-installation-on-freenas/ACL.png)

f. With the plugin still stopped, copy the old installation data folder from the old plugin Dataset to the new plugin Dataset

_**Note:** The `JAIL_ROOT` location will vary between different FreeNAS versions:_

+ FreeNAS 11.1 and bellow (warden) - `JAIL_ROOT=/mnt/[Volume]/jails/[JAIL_NAME]`
+ FreeNAS 11.2 and above (iocage) - `JAIL_ROOT=/mnt/[Volume]/iocage/jails/[JAIL_NAME]`

**Source for your old Plex plugin (warden)**

_If installed manually_

> `${JAIL_ROOT}/root/usr/local/plexdata/Plex Media Server/`

_If installed via plugin_

> `${JAIL_ROOT}/var/db/plexdata/Plex Media Server/`

**Destination (iocage)**

> `${JAIL_ROOT}/root/Plex Media Server/`

g. In the jails configuration menu, select the new Plex jail and add the mount point for the media folder. Try to keep the same path as the old jail so you won't have to edit your library. If you don't remember that the path was, you can access it by looking at the contents of `/mnt/[Volume]/jails/.[JAIL_NAME].meta/fstab`

```
# cat .plex.meta/fstab
/mnt/Volume1/Movies /mnt/Volume1/jails/plex//mnt/Media nullfs rw 0 0
```
<br>
h. Start the plugin and try to access it via web

![](/img/how-to-move-plex-installation-on-freenas/manage.png)

- - -

References:

+ [Move an Install to Another System](https://support.plex.tv/articles/201370363-move-an-install-to-another-system/)
+ [Installing Plex Media Server on FreeNAS](https://support.plex.tv/articles/installing-plex-media-server-on-freenas/)
+ [Where is the Plex Media Server data directory located?](https://support.plex.tv/articles/202915258-where-is-the-plex-media-server-data-directory-located/)
+ [FreeNAS® 11.3-U1 User Guide](https://www.ixsystems.com/documentation/freenas/11.3-U1/freenas.html)
+ [FreeNAS 11.3-RELEASE](https://www.ixsystems.com/blog/library/freenas-11-3-release/)

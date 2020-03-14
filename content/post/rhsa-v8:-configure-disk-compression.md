---
title: "RHCSA V8: Configure Disk Compression"
date: 2020-03-14T14:56:39-04:00
draft: false
author: "Victor Mendonça"
description: ""
tags: ["Linux", "RedHat", "RHCSA"]
---

#### Disclaimer

These are my study notes for the RHCSA exam on disk compression. There's most likely more information than what's needed for the exam, and I cannot guarantee that all information is correct.

<br>
## Definition

Virtual Data Optimizer (VDO) provides inline data reduction for Linux in the form of deduplication, compression, and thin provisioning. When you set up a VDO volume, you specify a block device on which to construct your VDO volume and the amount of logical storage you plan to present.  

> In the Red Hat Enterprise Linux 7.5 Beta, we introduced virtual data optimizer (VDO). VDO is a kernel module that can save disk space and reduce replication bandwidth. VDO sits on top of any block storage device and provides zero-block elimination, deduplication of redundant blocks, and data compression.  

VDO can be applied to a block device, and then normal disk operations can be applied to that device. LVM for example, can sit on top of VDO.

_Physical disk -> VDO -> Volumegroup -> Logical volume -> file system_

![](/img/rhsa-v8-configure-disk-compression/overview.png)

## Requirements and Recommendations

### Memory

Each VDO volume has two distinct memory requirements:  
<br>
#### The VDO module

VDO requires 370 MB of DRAM plus an additional 268 MB per each 1 TB of physical storage managed by the volume.  
<br>
#### The Universal Deduplication Service (UDS) index

UDS requires a minimum of 250 MB of DRAM, which is also the default amount that deduplication uses.  

The memory required for the UDS index is determined by the index type and the required size of the deduplication window:       

![](/img/rhsa-v8-configure-disk-compression/memory_requirements.png)

_**Note:** Sparse is the recommended configuration._

<br>
### Storage
<br>
#### **Logical Size**

Specifies the logical VDO volume size. The VDO Logical Size is how much storage we tell the OS that we have. Because of reduction and deduplication, this number will be bigger than the real physical size. This ratio will vary according to the type of data that is being stored (binary, video, audio, compressed data will have a very low ratio).  
<br>
#### Red Hat's Recommendation

**For active VMs or container storage**

_Use logical size that is ten times the physical size of your block device. For example, if your block device is 1TB in size, use 10T here._

**For object storage**

_Use logical size that is three times the physical size of your block device. For example, if your block device is 1TB in size, use 3T here._          

<br>
#### **Slab Size**

Specifies the size of the increment by which a VDO is grown. All of the slabs for a given volume will be of the same size, which may be any power of 2 multiple of 128 MB up to 32 GB. At least one entire slab is reserved by VDO for metadata, and therefore cannot be used for storing user data.  


The default slab size is 2 GB in order to facilitate evaluating VDO on smaller test systems. A single VDO volume may have up to 8096 slabs. Therefore, in the default configuration with 2 GB slabs, the maximum allowed physical storage is 16 TB. When using 32 GB slabs, the maximum allowed physical storage is 256 TB.

![](/img/rhsa-v8-configure-disk-compression/vdo_slab_sizes.png)

_The table above is from RHEL 7 documentation_

<br>
#### **Examples of VDO System Requirements by Physical Volume Size**

The following tables provide approximate system requirements of VDO based on the size of the underlying physical volume. Each table lists requirements appropriate to the intended deployment, such as primary storage or backup storage.    

![](/img/rhsa-v8-configure-disk-compression/primary_storage.png)

![](/img/rhsa-v8-configure-disk-compression/backup_storage.png)

<br>
### Deduplication, Indexing and Compression
<br>
#### Deduplication and Index

VDO uses a high-performance de-duplication index called UDS to detect duplicate blocks of data as they are being stored. The UDS index provides the foundation of the VDO product. For each new piece of data, it quickly determines if that piece is identical to any previously stored piece of data. If the index finds match, the storage system can then internally reference the existing item to avoid storing the same information more than once.

Deduplication is enabled by default.  

To disable deduplication during VDO block creation (so only compression is used), use the `--deduplication=disabled` option (you will not be able to use the `sparseIndex` option)

```
# vdo create --name=[name] --device=/dev/[device] --vdoLogicalSize=[VDO logical size] --deduplication=disabled --vdoSlabSize=[slab size]
```

To enable/disable deduplication on a existing block

```
# vdo enableDeduplication --name=my_vdo

# vdo disableDeduplication --name=my_vdo
```
<br>
#### Compression

In addition to block-level deduplication, VDO also provides inline block-level compression using the HIOPS Compression™ technology.  

VDO volume compression is on by default.

Compression operates on blocks that have not been identified as duplicates. When unique data is seen for the first time, it is compressed. Subsequent copies of data that have already been stored are deduplicated without requiring an additional compression step.

<br>
## Configuration Steps

Install 'vdo' (and if not installed by default 'kmod-vdo')

```
# yum install vdo
```

Start the service

```
# systemctl start vdo.service
```

Create the volume

```
# vdo create --name=[name] --device=/dev/[device] --vdoLogicalSize=[VDO logical size] --sparseIndex=enabled --vdoSlabSize=[slab size]
```

_Note: Using '--sparseIndex=disabled' will enable 'dense' indexing_

Optionally add LVM config, and/or create the file system (make sure to use the option to not discard blocks)

```
# mkfs.ext4 -E nodiscard /dev/mapper/[name]

# mkfs.xfs -K /dev/mapper/[name]
```

Update the system with the new device

```
# udevadm settle
```

Mount the device

```
# mount /dev/mapper/[name] /mount/point
```

To add it to '/etc/fstab'. You will need to add additional params so that systemd waits for VDO to start before mounting

```
# /dev/mapper/vdo-device /mount/point [fstype] defaults,_netdev,x-systemd.device-timeout=0,x-systemd.requires=vdo.service 0 0
```

See man pages for 'systemd.mount':

```nothing
x-systemd.device-timeout=
          Configure how long systemd should wait for a device to show up before
          giving up on an entry from /etc/fstab. Specify a time in seconds or
          explicitly append a unit such as "s", "min", "h", "ms".

x-systemd.requires=
          Configures a Requires= and an After= dependency between the created mount
          unit and another systemd unit, such as a device or mount unit.
```

<br>
## Administration

Check for real physical space usage  

```nothing
# vdostats --human-readable

Device              Size   Used   Available   Use%   Space Saving%
/dev/mapper/my_vdo  1.8T  407.9G    1.4T       22%       21%
```
<br>

- - -

**References:**

+ https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/system_design_guide/deploying-vdo_system-design-guide
+ https://www.linuxsysadmins.com/setting-up-virtual-data-optimizer-on-centos/
+ https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/system_design_guide/deploying-vdo_system-design-guide

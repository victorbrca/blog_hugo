---
title: "Testing New Hard Drives"
date: 2023-11-21T14:21:40-05:00
draft: false
author: "Victor Mendonça"
description: "A quick intro on Hard Drive burn-in tests"
tags: ["Hardware", "Storage", "Linux"]
---

So you got a spanking new hard drive for your NAS and you are ready to install it... but wait! What if the drive is bad?

![](https://i.imgur.com/XlwZRg7l.png)

This is not something that most people would think of, but that shiny new drive could already have come with defects (a.k.a extra features) from the factory. Or maybe it was part of a fun game of "throw the client's package" that some delivery man like to play (as my preferred social media likes to show me). So before we install this new piece of hardware that has the potential to render all my data, accumulated from years of hoarding, useless, let's do some testing.

## S.M.A.R.T Testing

Let's start with a S.M.A.R.T (Self-Monitoring, Analysis, and Reporting Technology) test.

![smart](https://i.imgur.com/4LQP5Iz.png)

> [Wikipedia: Self-Monitoring, Analysis and Reporting Technology (S.M.A.R.T.)](https://support-en.wd.com/app/answers/detailweb/a_id/39617/~/self-monitoring%2C-analysis-and-reporting-technology-%28s.m.a.r.t.%29)
>
> SMART is an interface between the platform's BIOS and the storage device. When SMART is enabled in the BIOS (mostly default), the BIOS can process information from the storage device and determine whether to send a warning message about potential failure of the storage device. The purpose of SMART is to warn a user of impending drive failure while there is still time to take action, such as backing up the data or copying the data to a replacement device.

First we need to identify if the drive is capable of S.M.A.R.T test. Most modern drives should be.

```none
sudo smartctl -i /dev/sdX
```

You should get an output similar to the one below:

```none
=== START OF INFORMATION SECTION ===
Device Model:     WDC WD60EFPX-68C5ZN0
Serial Number:    WD-WX12D12312345
LU WWN Device Id: 5 0014ee 26b395dd4
Firmware Version: 81.00A81
User Capacity:    6,001,175,126,016 bytes [6.00 TB]
Sector Sizes:     512 bytes logical, 4096 bytes physical
Rotation Rate:    5400 rpm
Form Factor:      3.5 inches
Device is:        Not in smartctl database 7.3/5528
ATA Version is:   ACS-3 T13/2161-D revision 5
SATA Version is:  SATA 3.1, 6.0 Gb/s (current: 6.0 Gb/s)
Local Time is:    Thu Nov  9 08:21:36 2023 EST
SMART support is: Available - device has SMART capability.
SMART support is: Enabled
```

I got the following error because I'm using a USB-C adapter:

```none
smartctl 7.4 2023-08-01 r5530 [x86_64-linux-6.5.8-arch1-1] (local build)
Copyright (C) 2002-23, Bruce Allen, Christian Franke, www.smartmontools.org

/dev/sda: Unknown USB bridge [0x14b0:0x0200 (0x100)]
Please specify device type with the -d option.

Use smartctl -h to get a usage summary
```

If that's the same for you, you can try using it with `-d sat`, and if your adapter is supported it should work.

```none
sudo smartctl -d sat -i /dev/sdX
```

Once we confirmed that the drive supports S.M.A.R.T. testing we can start. We are interested in the following three  tests:

+ **Short** - The goal of the short test is the rapid identification of a defective hard drive. Therefore, a maximum run time for the short test is 2 min
+ **Long** - The long test was designed as the final test in production and is the same as the short test with two differences. The first: there is no time restriction and in the Read/Verify segment the entire disk is checked and not just a section
+ **Conveyance Test** - This test can be performed to determine damage during transport of the hard disk within just a few minutes

We specify the test using the `-t` flag:

```none
smartctl -t [short|long|conveyance] [dev]
```

The test runs in the background and we can check it's status by greping `Self-test execution status`.

On the example below we can see that the test is in progress and that is 80% complete:

```none
$ sudo smartctl -a /dev/sda | grep -A 1 'Self-test execution status:'
Self-test execution status:      ( 242)	Self-test routine in progress...
					20% of test remaining.
```

We can use the same command with to check the test result. Just change the `-A` to '2' in `grep`:

```none
$ sudo smartctl -a /dev/sda | grep -A 2 'Self-test execution status:'
Self-test execution status:      (   0)	The previous self-test routine completed
					without error or no self-test has ever
					been run.
```

Another option is to use the `-l selftest` flag:

```none
$ sudo smartctl -l selftest /dev/sda
smartctl 7.4 2023-08-01 r5530 [x86_64-linux-6.5.8-arch1-1] (local build)
Copyright (C) 2002-23, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF READ SMART DATA SECTION ===
SMART Self-test log structure revision number 1
Num  Test_Description    Status                  Remaining  LifeTime(hours)  LBA_of_first_error
# 1  Short offline       Completed without error       00%        20         -
```

Also check the following string after each test:

```none
$ sudo smartctl -a /dev/sda | grep 'test result'
SMART overall-health self-assessment test result: PASSED
```

Now go ahead and run the short and conveyance tests (or all 3 if you have the time). Here's how long it took for me to run on a 6TB WD Red Plus (WD60EFPX) over USB-C (Nov 2023):

+ _conveyance_ -1m13s
+ _short_ - 2m
+ _long_ - 11h20m

If you have more than one hard drive to test, and you can plug them in at the same time, you can run the tests in parallel.

Once completed and you have confirmed they have passed, also check the thresholds at the end of the output. It will look similar to this:

```none
Vendor Specific SMART Attributes with Thresholds:
ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
  1 Raw_Read_Error_Rate     0x002f   200   200   051    Pre-fail  Always       -       0
  3 Spin_Up_Time            0x0027   100   253   021    Pre-fail  Always       -       0
  4 Start_Stop_Count        0x0032   100   100   000    Old_age   Always       -       2
  5 Reallocated_Sector_Ct   0x0033   200   200   140    Pre-fail  Always       -       0
  7 Seek_Error_Rate         0x002e   200   200   000    Old_age   Always       -       0
  9 Power_On_Hours          0x0032   100   100   000    Old_age   Always       -       46
 10 Spin_Retry_Count        0x0032   100   253   000    Old_age   Always       -       0
 11 Calibration_Retry_Count 0x0032   100   253   000    Old_age   Always       -       0
 12 Power_Cycle_Count       0x0032   100   100   000    Old_age   Always       -       1
192 Power-Off_Retract_Count 0x0032   200   200   000    Old_age   Always       -       0
193 Load_Cycle_Count        0x0032   200   200   000    Old_age   Always       -       3
194 Temperature_Celsius     0x0022   107   104   000    Old_age   Always       -       43
196 Reallocated_Event_Count 0x0032   200   200   000    Old_age   Always       -       0
197 Current_Pending_Sector  0x0032   200   200   000    Old_age   Always       -       0
198 Offline_Uncorrectable   0x0030   100   253   000    Old_age   Offline      -       0
199 UDMA_CRC_Error_Count    0x0032   200   200   000    Old_age   Always       -       0
200 Multi_Zone_Error_Rate   0x0008   100   253   000    Old_age   Offline      -       0
```

You want to pay attention to:

+ `Offline_Uncorrectable` - Damaged sectors that don't respond to any read/write requests (bad sectors). These sectors are remapped to spare sectors.
+ `Reallocated_Sector_Ct` - Count of damaged sectors that were remapped to spare sectors.
+ `Current_Pending_Sector` - Indicates the number of damaged sectors that are yet to be remapped or reallocated. This number could indicate that spare sectors are not available, and data from bad sectors can no longer be remapped.


## Badblocks

![Imgur](https://i.imgur.com/yPrwUU8m.png)

Before we continue, let's just make sure that you are indeed testing a new set of spinning rust (a.k.a. hard drive), and **not an SSD or an NVMe**. We don't want to run `badblocks` on the later 2.

### Overview

> [Arch Wiki: badblocks](https://wiki.archlinux.org/title/badblocks)
>
> S.M.A.R.T. (Self-Monitoring, Analysis, and Reporting Technology) is featured in almost every HDD still in use nowadays, and in some cases it can automatically retire defective HDD sectors. However, S.M.A.R.T. only passively waits for errors while badblocks can actively write simple patterns to every block of a device and then check them, searching for damaged areas (Just like memtest86* does with RAM).

Now that we have an understanding of what `badblocks` does, let's take some time to digest it. We will be writing to all blocks on your new hard drive and then reading to confirm that the data was written correctly. As if that wouldn't already take long, `badblocks` will do it not only once, but four times (with four different patterns).

> [Arch Wiki](https://wiki.archlinux.org/title/badblocks#Testing_for_bad_sectors)
>
> As the pattern is written to every accessible block, the device effectively gets wiped. The default is an extensive test with four passes using four different patterns: `0xaa` (10101010), `0x55` (01010101), `0xff` (11111111) and `0x00` (00000000). For some devices this will take a couple of days to complete.

As with `smartctl`, you can run multiple instances of `badblocks` in parallel if you have multiple disks. You can also shorten the time of the test by increasing the number of blocks that are tested at time (`-c`), or by specifying a single pattern to be written with the `-t` option, e.g.: `-t '0xaa'`, which will force it to do only one pass. If you specify multiple patterns, e.g.: `-t '0xaa' -t '0x55'`, you will be essentially running multiple passes.

Another option is to use the random pattern option with `-t random`. This will make `badblocks` use random patterns for the test, with only one pass (unless you specify `-p`).

Just keep in mind that different patterns (used by the default write-mode) work better because you can validate against stuck bits. But based on the amount of drives, available drive buses, and time that you have, you might not actually be able to run the full test. But that's a decision that only you can make.

I wanted to time my tests to help you making a decision, but as I ran them over a USB-C adapter my badblocks seem to have maxed out at around 41mb/s:

> 		Total DISK READ :       0.00 B/s | Total DISK WRITE :      41.74 M/s
> 		Actual DISK READ:       0.00 B/s | Actual DISK WRITE:      41.74 M/s
>   		 TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN     IO>    COMMAND                                                          
> 		1516251 be/4 root        0.00 B/s   41.74 M/s  ?unavailable?  badblocks -wsvb 4096 -t 0x00 /dev/sda -o badblocks-output.log

With that in mind, here are the timings from my latest test on a 6TB WD Red Plus (WD60EFPX) over USB-C (Nov 2023):

+ _Write mode_ - 83h
+ _Random write mode_ - 81h
+ _Write mode one pattern_ - 80h

And spoiler alert... we will be running the long test in `smartctl` once `badblocks` finishes. So also take that into account.

### Running the Test

First, let's take a look at what your drive's recommended blocksize is:

```none
sudo blockdev --getbsz /dev/sdX
```

Because this test will run for a while, start your preferred terminal multiplexer (e.g.: `screen`, `tmux`), change into root, and run `badblocks`:

```none
time badblocks -wsvb {blocksize} /dev/sdX -o [output_file]
```

- `time` a separate command to tell you the actual time `badblocks` ran for once complete.
- `-w` uses write-mode test, which is a **destructive action**.
- `-s` shows an estimate of the progress of the scan. This isn't 100% accurate, but it's better to see something than nothing at all.
- `-b {blocksize}` specify the block size. Be sure to replace `{blocksize}` with the number you found with the previous command mentioned (`blockdev --getbsz /dev/sdX`).
- `/dev/sdX` the drive you want to test. Replace with the actual drive. Be extra careful as you don't want to accidentally destroy data on the wrong disk.
- `-v` option is verbose mode
- `-o` option is output file. Without the `-o` `badblocks` will simply use the `STDOUT`


## S.M.A.R.T. Again

Once the `badblocks` test is complete, run another long `smartctl` test and check to make sure that everything is still good.

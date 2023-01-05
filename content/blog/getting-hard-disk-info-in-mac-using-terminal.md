+++
title = "Getting Hard Disk Info in Mac Using Terminal"
description = ""
date = 2021-11-16T15:07:38+05:30
tags = ["macosx", "terminal", "shell", "beginners"]
categories = []
+++

Its pretty easy. Just run the `diskutil` like the following

## Command

```shell
diskutil info disk0
```

## Output

```
   Device Identifier:         disk0
   Device Node:               /dev/disk0
   Whole:                     Yes
   Part of Whole:             disk0
   Device / Media Name:       APPLE SSD AP0256M

   Volume Name:               Not applicable (no file system)
   Mounted:                   Not applicable (no file system)
   File System:               None

   Content (IOContent):       GUID_partition_scheme
   OS Can Be Installed:       No
   Media Type:                Generic
   Protocol:                  PCI-Express
   SMART Status:              Verified

   Disk Size:                 251.0 GB (251000193024 Bytes) (exactly 490234752 512-Byte-Units)
   Device Block Size:         4096 Bytes

   Media OS Use Only:         No
   Media Read-Only:           No
   Volume Read-Only:          Not applicable (no file system)

   Device Location:           Internal
   Removable Media:           Fixed

   Solid State:               Yes
   Virtual:                   No
```
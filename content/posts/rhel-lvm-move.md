+++
title = "RHEL Move LVM Volume"
date = "2023-04-21T22:24:59+09:30"
author = "Adam"
cover = ""
tags = ["rhel", "LVM"]
keywords = ["rhel", "homelab"]
description = "How to move an LVM volume to a new drive"
showFullContent = false
+++

# How to move an LVM volume to a new drive  
When setting up my NAS with RHEL 9.1 I wanted to move the initial install to a new disk  

By default RHEL 9.x installs to disk by creating 3 partitions:  
* /boot/efi
* /boot
* LVM
  * Volume Group = 'rhel'
    * Logical Volume = 'root'
    * Logical Volume = 'swap'  

I'm happy to leave the boot partitions as they are but I want to move the LVM VG 'rhel' to a new disk!

**SOURCE** disk is `/dev/sdb[3]`  
**DESTINATION** disk is `/dev/sda`

## Extend LVM Volume Group to include the destination disk
`sudo vgextend rhel /dev/sda`

## Move the data from source disk to destination disk
`sudo pvmove /dev/sdb3 /dev/sda`

## Remove the source disk from the Volume Group
`sudo vgreduce rhel /dev/sdb3`

## Remove the source disk from Physical Volumes
`sudo pvremove /dev/sdb3`

## Extend the volume to fill the destination disk and resize the filesystem
`sudo lvextend -r -l+100%FREE /dev/rhel/root`

### Done!
There should be no need to update */etc/fstab* as the existing entry will be pointing to the LVM volume eg. */dev/mapper/rhel-root* which on the physical level is now already pointing to our new disk with the migrated data

---
title: "Homelab: Moving Raspberry OS to a larger SD Card"
date: 2025-01-22
toc: true
categories:
  - Blog
tags:
  - Homelab
---

I'm tweaking my setup to reduce reliance on the noisy external hard drive. I want to keep more data on the SD card, which is why I decided to move up from 16GB to 64GB.

<!--more-->

This post is a part of my _Homelab Series_. [See the index here](/Homelab-Introduction).
{: .notice}

## Backup and Restore

Insert the original, small SD card (with Raspberry OS on it). Run the following command to figure out its `/dev/diskN` index:

```bash
diskutil list
```

On my system, it is `/dev/disk4`. Run the following to back up the card to `pi.img` file on your desktop.

Note that command below uses `rdisk` instead of `disk`[^1].
{: .notice}

[^1]: "r" refers to **raw character device** for disk 4. It bypasses system's buffer cache, which means `dd` is able to run faster.

```bash
sudo dd if=/dev/rdisk4 of=/Users/$USER/Desktop/pi.dmg status=progress
```

If successful, command output should show backup progress in real time:

```conf
1371570688 bytes (1372 MB, 1308 MiB) transferred 158.002s, 8681 kB/s
```

**Warning!** It is quite a slow process. You may want to run `caffeinate` to stop your computer from going to sleep.
{: .notice}

When backup has completed, insert the new SD card, format it and use pretty much the previous command, just reversed. Make sure to check your `/dev/diskN` index first.

```bash
sudo dd if=/Users/$USER/Desktop/pi.dmg of=/dev/rdisk4 status=progress
```

I ran into an issue because Spotlight started auto-indexing the card, which made `dd` error out with an `dd: /dev/disk4: Resource busy` message. The solution was to unmount the main partition:

```bash
diskutil umount /dev/disk4s1
```

## Resize Partition

The way `dd` works, Pi will think the new SD card is the same size as the old one (14.4G):

```conf
Device         Boot   Start      End  Sectors  Size Id Type
/dev/mmcblk0p1         8192  1056767  1048576  512M  c W95 FAT32 (LBA)
/dev/mmcblk0p2      1056768 31354879 30298112 14.4G 83 Linux
```

We need to resize the partition! Once Pi boots up with the new SD card in, ssh into it and run the Configuration Tool:

```bash
sudo raspi-config
```

Once it loads, go to `6 Advanced Options` and then choose `A1 Expand Filesystem`. Let it do it's thing and once it's done, exit Configuration Tool. It will ask you to reboot, which you should absolutely do at this point.

Check if it worked with `sudo fdisk -l`:

```conf
Device         Boot   Start       End   Sectors  Size Id Type
/dev/mmcblk0p1         8192   1056767   1048576  512M  c W95 FAT32 (LBA)
/dev/mmcblk0p2      1056768 124735487 123678720   59G 83 Linux
```

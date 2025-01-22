---
title: "Homelab: Moving Raspberry OS to a larger SD Card"
date: 2025-01-22
categories:
  - Blog
tags:
  - Homelab
---

This post is a part of my _Homelab Series_. [See the index here](/Homelab-Introduction).
{: .notice}

Insert the source SD card (with Raspberry OS on it). Run the following command to figure out its `/dev/diskN` index:

```bash
diskutil list
```

On my system, it is `/dev/disk4`. Run the following to back up the card to `pi.img` file on your desktop:

```bash
sudo dd if=/dev/disk4 of=/Users/$USER/Desktop/pi.dmg status=progress
```

If successful, command output should show backup progress in real time:

```
1371570688 bytes (1372 MB, 1308 MiB) transferred 158.002s, 8681 kB/s
```

Warning: it is quite a slow process. You may want to run `caffeinate` to stop your computer from going to sleep.
{: .notice--warning}

When backup has completed, insert the new SD card, format it and use pretty much the previous command, just reversed. Make sure to check your `/dev/diskN` index first.

```bash
sudo dd if=/Users/$USER/Desktop/pi.dmg of=/dev/disk4 status=progress
```

I ran into an issue because Spotlight started auto-indexing the card, which made `dd` error out with an `dd: /dev/disk4: Resource busy` message. The solution was to unmount the main partition:

```bash
diskutil umount /dev/disk4s1
```
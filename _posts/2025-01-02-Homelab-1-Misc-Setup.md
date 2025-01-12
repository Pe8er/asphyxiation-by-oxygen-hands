---
title: "Homelab #1: Misc Setup"
date: 2025-01-02
# last_modified_at: 2025-01-09
categories:
  - Blog
tags:
  - Homelab
toc: true
---

An assortments of steps I went through when setting up my Pi.

<!--more-->

This post is a part of my *Homelab Series*. [See the index here](/Homelab-0-Introduction).
{: .notice}


## Define Static IP

Figure out pi's current IP address:

```bash
hostname -I
```

Open the relevant configuration file:

```bash
sudo nano /boot/firmware/cmdline.txt
```

Add the IP you want at the end. Doesn't need to be the one you found above.

```bash
ip=192.168.1.132
```

## SSH without Password

Generate keys on machine that you will be SSH'ing from:

```bash
ssh-keygen -t rsa
```

Copy keys to pi:

```bash
cat ~/.ssh/id_rsa.pub | ssh [USERNAME]@[PI IP ADDRESS] "cat >> ~/.ssh/authorized_keys"
```


## Disable Wi-fi

Open the relevant configuration file:

```bash
sudo nano /boot/firmware/cmdline.txt
```

Add at the end of the first (and only) line:

```bash
dtoverlay=disable-wifi
```

## Samba Share for Easy File Management

Install Samba:

```bash
sudo apt install samba samba-common-bin
```

Create the configuration file:

```bash
sudo nano /etc/samba/smb.conf
```

Paste the following, replacing `path` with the path where the drive you want to share is mounted (`/mnt/media` in my case):

```
[SHARENAME]
path = /path/to/folder
writeable = yes
browseable = yes
public=yes
```

Set up password for your user:

```bash
sudo smbpasswd -a <USERNAME>
```

Restart the service to get it going:

```bash
sudo systemctl restart smbd
```


## Mount External Hard Drive

Find your drive. It should be at the very bottom, named most likely /dev/sdaN.
```bash
sudo fdisk -l
```
Find UUID of your /dev/sdaN:
```bash
sudo ls -l /dev/disk/by-uuid/
```
Create a folder where the drive will be mounted:
```bash
sudo mkdir /mnt/[NAME]
```
Add drive info to the scary auto-mounting configuration file:
```bash
sudo nano /etc/fstab
```
Add new line:
```bash
UUID=[UUID] /mnt/[NAME] [TYPE] defaults,auto,users,rw,nofail,noatime 0 0
```
Restart the service, unmount, re-mount the drive and reboot to check if everything works:

```bash
systemctl daemon-reload
```
```bash
sudo umount /dev/sdaN
```
```bash
sudo mount -a
```
```bash
sudo reboot
```

## Fix Permissions

I think I did this to get transmission to write to the external drive.

```bash
sudo su
```
```bash
find [YOURDRIVEPATH] -type d -exec chmod 755 {} \;
```
```bash
find [YOURDRIVEPATH] -type f -exec chmod 644 {} \;
```
---
title: "Reducing External Hard Drive Noise"
date: 2025-01-06
categories:
  - Blog
tags:
  - Homelab
---

I have a 12 TB WD Essentials USB hard drive connected to my Raspberry Pi 3B. It's annoyingly loud, so I needed a solution to spin it down when not in use. There are several ways to do it, I found that [hd-idle](https://github.com/adelolmo/hd-idle?tab=readme-ov-file#run-hd-idle) worked for me.

<!--more-->

This post is a part of my _Homelab Series_. [See the index here]({%- post_url 2025-01-01-Homelab-Introduction -%}).
{: .notice}

## Install

Download the package for pi:

```bash
curl -s https://api.github.com/repos/adelolmo/hd-idle/releases/latest | awk -F\" '/browser_download_url.*.arm64.deb/{system("curl -OL " $(NF-1))}'
```

Install it:

```bash
sudo dpkg -i ./hd-idle*.deb
```

## Config

Edit the config file:

```bash
sudo nano /etc/default/hd-idle
```

And make the following changes:

```conf
START_HD_IDLE=true
HD_IDLE_OPTS="-i 0 -a /dev/sda -i 600 -l /var/log/hd-idle.log"
```

Where `/dev/sda` is the hard drive you want to spin down after `600` seconds (=10 minutes).

## Enable Service

```bash
sudo systemctl enable hd-idle
```

```bash
sudo systemctl start hd-idle
```

## Check Logs

```bash
sudo cat /var/log/hd-idle.log
```

```bash
journalctl -u hd-idle.service | grep hd-idle| tail -n 20
```

```bash
sudo systemctl status hd-idle.service | tail -n 20
```

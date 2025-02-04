---
title: "Plex Media Server"
date: 2025-01-04
header:
  teaser: /assets/images/plex.jpg
categories:
  - Blog
tags:
  - Homelab
toc: true
---

![Plex Screenshot](/assets/images/plex.jpg)
{: .screenshot}

I use Plex Media Server as my personal, local/cloud music library. I have a lifetime Plex Pass, which gives me access to all [Plexamp](https://www.plex.tv/plexamp/) features, including setting it up as a headless player on my Pi.

<!--more-->

This post is a part of my _Homelab Series_. [See the index here]({%- post_url 2025-01-01-Homelab-Introduction -%}).
{: .notice}

## Plex Media Server

First, install a dependency:

```bash
sudo apt-get install apt-transport-https
```

Add PMS repositories to `apt` directory:

```bash
curl https://downloads.plex.tv/plex-keys/PlexSign.key | gpg --dearmor | sudo tee /usr/share/keyrings/plex-archive-keyring.gpg >/dev/null
```

```bash
echo deb [signed-by=/usr/share/keyrings/plex-archive-keyring.gpg] https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
```

```bash
sudo apt-get update
```

And install!

```bash
sudo apt install plexmediaserver
```

Plex Media Server is available:

```conf
http://[PI IP]:32400/web/
```

## Headless Plexamp

This allows the Pi to act as a Plex player when connected to MiniDSP SHD and my speakers. I found the [full installation instructions here.](https://gist.github.com/tgp-2/fc34c5389bc3e4ef332e28d9430b0ebf)

Get the installer:

```bash
wget https://gist.githubusercontent.com/tgp-2/65e6f2f637bc81df2c9fd9ba33f73bc6/raw/e7e9e47046c29a6090042a9a0a868a5bf7cf48be/plexamp-install.sh
```

Run it:

```bash
bash ./plexamp-install.sh
```

After the installation has been completed, reboot and Plexamp should start automatically.

```bash
sudo reboot
```

Open Plexamp and configure it:

```conf
http://[PI IP]:32500
```

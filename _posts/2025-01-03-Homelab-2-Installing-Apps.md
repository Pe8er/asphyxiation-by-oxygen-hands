---
title: "Homelab #2: Installing Apps"
date: 2025-01-03
# last_modified_at: 2025-01-09
categories:
  - Blog
tags:
  - Homelab
toc: true
---

I run a bunch of apps on my amazing _Pi homelab_ via Docker.

<!--more-->

This post is a part of my _Homelab Series_. [See the index here](/Homelab-0-Introduction).
{: .notice}

## Docker

Make sure Pi is up to date:

```bash
sudo apt update
```

```bash
sudo apt upgrade
```

Proceed to install Docker:

```bash
curl -sSL https://get.docker.com | sh
```

Add your user to `docker` group:

```bash
sudo usermod -aG docker $USER
```

Check if it worked:

```bash
logout
```

```bash
groups
```

Now, test if Docker works:

```bash
docker run hello-world
```

If it worked, Terminal should show the following message:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

## Portainer

I'm a sucker for GUI, so I needed this to get things going. First, pull the Portainer image:

```bash
sudo docker pull portainer/portainer-ce:latest
```

Set up the container:

```bash
sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

Portainer should be available:

```bash
http://[PI IP]:9000
```

## Transmission

The one and only [haugene/docker-transmission-openvpn](https://github.com/haugene/docker-transmission-openvpn).

```bash
version: '3.3'
services:
    transmission-openvpn:
        cap_add:
            - NET_ADMIN
        volumes:
            - '/mnt/media/Piotrek/Downloads:/data'
            - '/home/pe8er/docker/transmission:/config'
            - '/mnt/media/Piotrek/Downloads/Completed:/completed'
            - '/mnt/media/Piotrek/Downloads/Incomplete:/incomplete'
            - '/mnt/media/Piotrek/Downloads/Watch:/watch'
        environment:
            - OPENVPN_PROVIDER=PIA
            - OPENVPN_CONFIG=poland,germany,france,sweden
            - OPENVPN_USERNAME=[USER]
            - OPENVPN_PASSWORD=[PASS]
            - LOCAL_NETWORK=192.168.1.0/24
            - CREATE_TUN_DEVICE=true
            - OPENVPN_OPTS=--inactive 3600 --ping 10 --ping-exit 60
            - PUID=1000
            - PGID=1000
            - TZ=Europe/Warsaw
            - TRANSMISSION_DOWNLOAD_DIR=/completed
            - TRANSMISSION_HOME=/config/transmission-home
            - TRANSMISSION_INCOMPLETE_DIR=/incomplete
            - TRANSMISSION_WATCH_DIR=/watch
            - TRANSMISSION_SCRIPT_TORRENT_ADDED_ENABLED=true
            - TRANSMISSION_SCRIPT_TORRENT_ADDED_FILENAME=/config/transmission-home/scripts/transmission-add.sh
        logging:
            driver: json-file
            options:
                max-size: 10m
        ports:
            - '9091:9091'
        image: haugene/transmission-openvpn
```

### _Torrent Added Notification_ Script for Pushover

```bash
#!/bin/bash

# Send push notification to pushover device when a torrent is complete.
#
# Available environment variables from Transmission (as of v2.83) are:
#
# TR_APP_VERSION
# TR_TORRENT_DIR
# TR_TORRENT_HASH
# TR_TORRENT_ID
# TR_TIME_LOCALTIME
# TR_TORRENT_NAME

# Pushover Token and User Key
TOKEN_USER="[TOKEN_USER]";
TOKEN_APP="[TOKEN_APP]";

# Message for the notification.
MESSAGE="$TR_TORRENT_NAME added to Transmission.";

PRIORITY=0;
SOUND="tugboat";
TITLE="☠️ Torrent added";

TIMESTAMP=$(date +%s);

curl -s --form-string "token=$TOKEN_APP" --form-string "user=$TOKEN_USER" --form-string "timestamp=$TIMESTAMP" --form-string "priority=$PRIORITY" --form-string "sound=$SOUND" --form-string "title=$TITLE" --form-string "message=$MESSAGE" https://api.pushover.net/1/messages.json
```

## prowlarr

Image is grabbed from [linuxserver/prowlarr](https://docs.linuxserver.io/images/docker-prowlarr/#docker-cli-click-here-for-more-info).

```bash
---
services:
  prowlarr:
    image: lscr.io/linuxserver/prowlarr:latest
    container_name: prowlarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Warsaw
    volumes:
      - /home/pe8er/docker/prowlarr:/config
    ports:
      - 9696:9696
    restart: unless-stopped
```

## lidarr

Image is from [linuxserver/docker-lidarr](https://github.com/linuxserver/docker-lidarr).

```bash
---
services:
  lidarr:
    image: lscr.io/linuxserver/lidarr:latest
    container_name: lidarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Warsaw
    volumes:
      - /home/pe8er/docker/lidarr:/config
      - /mnt/media/Piotrek/.config/lidarr/MediaCover:/config/MediaCover #because media covers don't fit on the SD card
      - /mnt/media/Piotrek/Downloads/Completed:/downloads
      - /mnt/media/Piotrek/Music:/music
    ports:
      - 8686:8686
    restart: unless-stopped
```
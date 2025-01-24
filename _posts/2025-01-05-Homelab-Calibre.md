---
title: "Homelab: Calibre Web"
date: 2025-01-05
categories:
  - Blog
tags:
  - Homelab
toc: true
header:
  teaser: /assets/images/calibre.jpg
---

![Calibre Web Screenshot](/assets/images/calibre.jpg)
{: .screenshot}

I synchronize my Calibre library between my Mac and Pi using Syncthing. I add and manage books on Mac and do a one way sync to Pi to prevent database corruption. And then [Calibre-Web](https://github.com/janeczku/calibre-web) on Pi picks up the database and makes it available on my local network.

<!--more-->

At some point, I'll probably use it as a syncing mechanism for my Kobo e-reader.

This post is a part of my _Homelab Series_. [See the index here](/Homelab-Introduction).
{: .notice}

## Calibre Web

Docker image is from [linuxserver/docker-calibre-web](https://github.com/linuxserver/docker-calibre-web). To install, paste the following docker compose to portainer and customize as needed:

```yaml
---
services:
  calibre-web:
    image: lscr.io/linuxserver/calibre-web:latest
    container_name: calibre-web
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Warsaw
      - CALIBRE_RECONNECT=true # This is required for the automatic database reload script to work.
    volumes:
      - /home/pe8er/docker/calibre-web:/config
      - /mnt/media/Piotrek/CalibreLibrary:/books
    ports:
      - 8083:8083
    restart: unless-stopped
```

## Automatic Database Reload

Calibre Web doesn't reload automatically when changes are made to it's database — you need to restart it or reload it from the admin panel manually. This script automates this process.

Before you start, make sure you have this environment variable in your Calibre-Web container:

```conf
CALIBRE_RECONNECT=true
```

Install the notifications system:

```bash
sudo apt update
```

```bash
sudo apt install inotify-tools
```

Create monitoring script:

```bash
nano /home/$USER/docker/calibre-web/calibre-monitor.sh
```

Paste the following:

```bash
#!/bin/bash

inotifywait -m -e modify /mnt/media/Piotrek/CalibreLibrary | # Replace path with folder where your metadata.db is located.
while read -r events filepath; do
        echo "$events"
        echo "$filepath"
        curl localhost:8083/reconnect
done
```

Make the script executable:

```bash
chmod a+x /home/$USER/docker/calibre-web/calibre-monitor.sh
```

Set up a service so it persists across reboots:

```bash
sudo nano /etc/systemd/system/calibre-monitor.service
```

Paste the following and insert your username where indicated:

```yaml
[Unit]
Description=Calibre Database Monitor

[Service]
Type=simple
Restart=always
RestartSec=5
ExecStart=/bin/bash /home/[YOUR USERNAME]/docker/calibre-web/calibre-monitor.sh
User=[YOUR USERNAME]
WorkingDirectory=/home/[YOUR USERNAME]

[Install]
WantedBy=multi-user.target
```

Enable service:

```bash
sudo systemctl daemon-reload
```

```bash
sudo systemctl enable calibre-monitor.service
```

```bash
sudo systemctl start calibre-monitor.service
```

Check if it works:

```bash
sudo systemctl status calibre-monitor.service
```

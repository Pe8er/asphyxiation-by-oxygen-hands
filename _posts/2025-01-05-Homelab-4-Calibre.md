---
title: "Homelab #4: Calibre Web"
date: 2025-01-05
# last_modified_at: 2025-01-09
categories:
  - Blog
tags:
  - Homelab
toc: true
---

I'm syncing my Calibre library from my Mac to Pi using Syncthing. And then [Calibre-Web](https://github.com/janeczku/calibre-web) picks it up and makes it available on my local network. I'm planning to use it as a syncing mechanism for my Kobo e-reader.

<!--more-->

This post is a part of my _Homelab Series_. [See the index here](/Homelab-0-Introduction).
{: .notice}

## Calibre Web

Docker image is from [linuxserver/docker-calibre-web](https://github.com/linuxserver/docker-calibre-web).

```bash
---
services:
  calibre-web:
    image: lscr.io/linuxserver/calibre-web:latest
    container_name: calibre-web
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Warsaw
      - CALIBRE_RECONNECT=true
    volumes:
      - /home/pe8er/docker/calibre-web:/config
      - /mnt/media/Piotrek/CalibreLibrary:/books
    ports:
      - 8083:8083
    restart: unless-stopped
```

## Database Automatic Reload

These steps make Calibre Web reload its database connection automatically, whenever any change to the database (incoming from my Mac) is detected.

Install the notifications system:

```bash
sudo apt update
```

```bash
sudo apt install inotify-tools
```

Create monitoring script:

```bash
nano /home/pe8er/docker/calibre-web/calibre-monitor.sh
```

Paste the following:

```bash
#!/bin/bash

inotifywait -m -e modify /mnt/media/Piotrek/CalibreLibrary |
while read -r events filepath; do
        echo "$events"
        echo "$filepath"
        curl localhost:8083/reconnect
done
```

Make the script executable:

```bash
chmod a+x /home/pe8er/docker/calibre-web/calibre-monitor.sh
```

Set up a service so it persists across reboots:

```bash
sudo nano /etc/systemd/system/calibre-monitor.service
```

Paste the following:

```bash
[Unit]
Description=Calibre Database Monitor

[Service]
Type=simple
Restart=always
RestartSec=5
ExecStart=/bin/bash /home/pe8er/docker/calibre-web/calibre-monitor.sh
User=pe8er
WorkingDirectory=/home/pe8er

[Install]
WantedBy=multi-user. target
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
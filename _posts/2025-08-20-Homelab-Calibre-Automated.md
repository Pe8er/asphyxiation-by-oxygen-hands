---
title: "Calibre Web Automated"
date: 2025-08-20
categories:
  - Blog
tags: [Homelab]
toc: true
header:
  teaser: /assets/images/calibre.jpg
---

![Calibre Web Screenshot](/assets/images/calibre.jpg)
{:.screenshot}

This is a follow-up to my post about setting up Calibre book library on my local server.

<!--more-->

This post is a part of my *Homelab Series*. [See the index here]({%- post_url 2025-01-01-Homelab-Introduction -%}).
{:.notice}

## Installation

```yaml
---
secrets:
  hardcover-api:
    file: /home/pe8er/.secrets/hardcover
services:
    calibre-web-automated:
        image: crocodilestick/calibre-web-automated:latest
        container_name: calibre-web-automated
        secrets:
    	    - hardcover-api
        environment:
            - PUID=1000
            - PGID=1000
            - TZ=Europe/Warsaw
            - HARDCOVER_TOKEN=/run/secrets/hardcover-api
        volumes:
            - /home/pe8er/docker/cwa:/config
            - /home/pe8er/Downloads/Completed:/cwa-book-ingest
            - /mnt/ssd-sandisk/CalibreLibrary:/calibre-library
        ports:
            - 8083:8083 
        restart: unless-stopped
```

Default login credentials:

**Username:** admin
**Password:** admin123

## Post Install

1. Disable password requirements: **Basic Configuration → Security Settings →  User Password Policy**
2. Change admin password
3. Enable uploads: **Basic Configuration → Feature Configuration → Enable Uploads**
4. Enable Kobo and Hardcover sync: same panel as above
5. [Enable Kobo integration](https://github.com/janeczku/calibre-web/wiki/Kobo-Integration)

## Bonus: [Calibre Downloader](https://github.com/calibrain/calibre-web-automated-book-downloader)

This apps lets me search and download books from annas-archive.org, and adds them to CWA automatically.

1. Make a folder for it:

```bash
mkdir /home/pe8er/docker/cwa-downloader
cd /home/pe8er/docker/cwa-downloader
```

2. Get the docker-compose.yml:

```bash
curl -O https://raw.githubusercontent.com/calibrain/calibre-web-automated-book-downloader/refs/heads/main/docker-compose.yml
```

```yaml
services:
  calibre-web-automated-book-downloader:
    image: ghcr.io/calibrain/calibre-web-automated-book-downloader:latest
    # Uncomment to build the image from the Dockerfile for local testing changes.
    # Remember to comment out the image line above.
    #build: .
    container_name: cwa-downloader
    environment:
      FLASK_PORT: 8084
      LOG_LEVEL: info
      BOOK_LANGUAGE: pl,en
      SUPPORTED_FORMATS: epub
      USE_BOOK_TITLE: true
      TZ: Europe/Warsaw
      APP_ENV: prod
      UID: 1000
      GID: 100
      # CWA_DB_PATH: /auth/app.db  # Comment out to disable authentication
      # Queue management settings
      MAX_CONCURRENT_DOWNLOADS: 3
      DOWNLOAD_PROGRESS_UPDATE_INTERVAL: 5
    ports:
      - 8084:8084
    restart: unless-stopped
    volumes:
      # This is where the books will be downloaded to, usually it would be
      # the same as whatever you gave in "calibre-web-automated"
      - /home/pe8er/Downloads/Books:/cwa-book-ingest
      # This is the location of CWA's app.db, which contains authentication
      # details. Comment out to disable authentication
      # - /home/pe8er/docker/cwa/app.db:/auth/app.db:ro
```

3. Start the service:

```bash
docker compose up -d
```

4. Access the web interface at `http://192.168.1.199:8084`

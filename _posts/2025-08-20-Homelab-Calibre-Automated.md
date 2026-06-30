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

![Calibre Web Screenshot](/assets/images/cwa.jpg)
{:.screenshot}

This is a follow-up to my post about setting up Calibre book library on my local server.<!--more--> I found a better way!

This post is a part of my *Homelab Series*. [See the index here]({%- post_url 2025-01-01-Homelab-Introduction -%}).
{:.notice}

## CWA Installation

[Calibre Web Automated](https://github.com/crocodilestick/Calibre-Web-Automated) is the final (so far) form of modernizing the OG book management app, Calibre. It runs in docker and it has tons of QOL improvements.

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

- **Username:** admin
- **Password:** admin123

## Post Install

1. Disable password requirements: **Basic Configuration → Security Settings →  User Password Policy**
2. Change admin password
3. Enable uploads: **Basic Configuration → Feature Configuration → Enable Uploads**
4. Enable Kobo and Hardcover sync: same panel as above
5. [Enable Kobo integration](https://github.com/janeczku/calibre-web/wiki/Kobo-Integration)

## [Calibre Downloader](https://github.com/calibrain/calibre-web-automated-book-downloader) (aka Shelfmark aka Litfinder)

This apps lets me search and download books from annas-archive.org, and adds them to CWA automatically.

1. Make a folder for it:

```bash
mkdir /home/pe8er/docker/cwa-downloader
cd /home/pe8er/docker/cwa-downloader
```

2. Get the docker-compose.yml:

```bash
curl -O https://raw.githubusercontent.com/calibrain/shelfmark/main/compose/docker-compose.yml
```

```yaml
services:
  calibre-web-automated-book-downloader:
    image: ghcr.io/calibrain/shelfmark:latest
    # Uncomment to build the image from the Dockerfile for local testing changes.
    # Remember to comment out the image line above.
    #build: .
    container_name: shelfmark
    environment:
      TZ: Europe/Warsaw
      UID: 1000
      GID: 100
    ports:
      - 8084:8084
    restart: unless-stopped
    volumes:
      - /home/pe8er/Downloads/Books:/cwa-book-ingest
      - /mnt/ssd-internal/Audiobooks:/Audiobooks
      - /home/pe8er/docker/cwa-downloader:/config
```

3. Start the service:

```bash
docker compose up -d
```

4. Access the web interface at `http://192.168.1.199:8084`

Nice! Downloading works well and books show up in Calibre automatically. Next step:

## Kobo Integration

There is a way to seamlessly display Calibre library on Kobo e-readers. It works very well and it's pretty damn easy to do!

1. Connect Kobo to computer via USB-C.
2. Open CWA -> Account dropdown -> Admin -> Kobo Sync Token -> Create / View
3. Open `.kobo/Kobo/Kobo eReader.conf` and add the generated token to it:
   
   `api_endpoint=http://192.168.1.199:8083/kobo/<hash>`
4. Hit "Force Full Kobo Sync"
5. Bob's your uncle!

## Mousehole

And there's [this](https://github.com/t-mart/mousehole).

```yaml
services:
  mousehole:
    image: tmmrtn/mousehole:latest
    network_mode: "container:transmission-openvpn"
    environment:
      MOUSEHOLE_AUTH_PASSWORD: [PASSWORD]
      TZ: Europe/Warsaw
    volumes:
      - mousehole:/home/pe8er/docker/mousehole
    restart: unless-stopped

volumes:
  mousehole:
```

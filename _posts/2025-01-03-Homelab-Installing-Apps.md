---
title: "Homelab: Installing Apps"
date: 2025-01-03
header:
  teaser: /assets/images/portainer.jpg
# last_modified_at: 2025-01-09
categories:
  - Blog
tags:
  - Homelab
toc: true
---

![Portainer screenshot](/assets/images/portainer.jpg)
{: .screenshot}

I run a bunch of apps on my Pi. Most of them via Docker.

<!--more-->

This post is a part of my _Homelab Series_. [See the index here]({%- post_url 2025-01-01-Homelab-Introduction -%}).
{: .notice}

## Syncthing

This is my preferred solution for private file syncing and backup. This package is allegedly needed for syncthing to work properly:

```bash
sudo apt install apt-transport-https
```

Download release keys:

```bash
curl -s https://syncthing.net/release-key.txt | gpg --dearmor | sudo tee /usr/share/keyrings/syncthing-archive-keyring.gpg >/dev/null
```

Add syncthing repository to local sources:

```bash
echo "deb [signed-by=/usr/share/keyrings/syncthing-archive-keyring.gpg] https://apt.syncthing.net/ syncthing stable" | sudo tee /etc/apt/sources.list.d/syncthing.list
```

And update `apt` so that syncthing can be installed:

```bash
sudo apt update
```

Install syncthing:

```bash
sudo apt install syncthing
```

Next, modify the configuration so syncthing can be accessed by other devices. Run it so it generates config files:

```bash
syncthing
```

And then `CTRL` + `C` to kill it. Edit configuration file:

```bash
nano ~/.local/state/syncthing/config.xml
```

Look for the following line (`CTRL` + `W`) and replace with the ([preferrably static](http://localhost:4000/Homelab-Misc-Setup/#define-static-ip)) IP of your Pi:

```conf
<address>192.168.1.132:8384</address>
```

Set up a service so syncthing launches on reboot:

```bash
sudo systemctl enable syncthing@$USER
```

And start the service:

```bash
sudo systemctl start syncthing@$USER
```

Syncthing is available:

```conf
http://[PI IP]:8384
```

## Docker

Docker is a fantastic solution to easily run lots of software in a quick, safe and replicable way.

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

```txt
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

## Portainer

I'm a sucker for GUI, so I need Portainer to get things going. First, pull the Portainer image:

```bash
sudo docker pull portainer/portainer-ce:latest
```

Set up the container:

```bash
sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

Portainer should be available:

```conf
http://[PI IP]:9000
```

## Transmission

The one and only [haugene/docker-transmission-openvpn](https://github.com/haugene/docker-transmission-openvpn).

```yaml
version: "3.3"
services:
  transmission-openvpn:
    cap_add:
      - NET_ADMIN
    volumes:
      - "/mnt/ssd-sandisk/Downloads:/data"
      - "/home/pe8er/docker/transmission:/config"
      - "/mnt/ssd-sandisk/Downloads/Completed:/completed"
      - "/mnt/ssd-sandisk/Downloads/Incomplete:/incomplete"
      - "/mnt/ssd-sandisk/Downloads/Watch:/watch"
    environment:
      - OPENVPN_PROVIDER=PIA
      - OPENVPN_CONFIG=poland,germany,france,sweden
      - OPENVPN_USERNAME=[USER]
      - OPENVPN_PASSWORD=[PASSWORD]
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
      - "9091:9091"
    image: haugene/transmission-openvpn
```

### _Torrent Added Notification_ Script for Pushover

```bash
#!/bin/bash

# Send push notification to pushover device when a torrent is complete.
# Available environment variables from Transmission (as of v2.83) are:
#
# TR_APP_VERSION
# TR_TORRENT_DIR
# TR_TORRENT_HASH
# TR_TORRENT_ID
# TR_TIME_LOCALTIME
# TR_TORRENT_NAME

# Pushover Token and User Key
TOKEN_APP="[TOKEN_APP]";
TOKEN_USER="[TOKEN_USER]";
TIMESTAMP=$(date +%s);
PRIORITY=0;
SOUND="tugboat";
TITLE="☠️ Torrent added";
MESSAGE="$TR_TORRENT_NAME added to Transmission.";

curl -s --form-string "token=$TOKEN_APP" --form-string "user=$TOKEN_USER" --form-string "timestamp=$TIMESTAMP" --form-string "priority=$PRIORITY" --form-string "sound=$SOUND" --form-string "title=$TITLE" --form-string "message=$MESSAGE" https://api.pushover.net/1/messages.json
```

## prowlarr

Image is grabbed from [linuxserver/prowlarr](https://docs.linuxserver.io/images/docker-prowlarr/#docker-cli-click-here-for-more-info).

```yaml
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

```yaml
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
      - /mnt/ssd-sandisk/Downloads/Completed:/downloads
      - /mnt/ssd-sandisk/Music:/music
    ports:
      - 8686:8686
    restart: unless-stopped
```

## Magic of Remote Access: Tailscale

I could never figure out how VPNs work. I've had a hazy idea that's what I need to access all my apps from outside the house…but I would never attempt to set up a VPN server (??) manually. Thankfully a good friend is a seasoned devops admin and he proposed a solution appopriate for my highly incapable brain: [Tailscale](https://tailscale.com/). I found a tutorial, closed my eyes and proceeded copy-pasting stuff.

First, make sure `apt` is up to date:

```bash
sudo apt update
```

```bash
sudo apt upgrade
```

<small>Note: Tailscale needs two dependencies that I already had installed: `curl` and `apt-transport-https`.</small>

Grab Tailscale repo keys:

```bash
curl -fsSL https://pkgs.tailscale.com/stable/raspbian/bullseye.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg > /dev/null
```

Add Tailscale repository to the system:

```bash
curl -fsSL https://pkgs.tailscale.com/stable/raspbian/bullseye.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list
```

Update apt:

```bash
sudo apt update
```

And install:

```bash
sudo apt install tailscale
```

Start Tailscale:

```bash
sudo tailscale up
```

In order for other Tailscale clients to be able to access my LAN, Pi needs to be set up as _subnet router_. The following is from [Tailscale documentation](https://tailscale.com/kb/1019/subnets). First, enable port forwarding:

```bash
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
```

```bash
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
```

```bash
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

Start Tailscale with a flag that adds subnet routes:

```bash
sudo tailscale up --advertise-routes=192.168.1.0/24,198.51.100.0/24
```

<small>(I'm not entirely sure what `198.51…` is needed for but I'm too scared to change it now, since it seems to work well)</small>

Next, go to Tailscale admin console to 1) approve routes and 2) add access rules:

```json
{
  "groups": {
    "group:admin": ["[TAILSCALE EMAIL]@privaterelay.appleid.com"]
  },
  "acls": [
    {
      "action": "accept",
      "src": ["group:admin", "192.168.1.0/24", "198.51.100.0/24"],
      "dst": ["192.168.1.0/24:*", "198.51.100.0/24:*"]
    }
  ],
  "ssh": [
    // The default SSH policy, which lets users SSH into devices they own.
    // Learn more at https://tailscale.com/kb/1193/tailscale-ssh/
    {
      "action": "check",
      "src": ["autogroup:member"],
      "dst": ["autogroup:self"],
      "users": ["autogroup:nonroot", "root"]
    }
  ]
}
```

<!-- ## Mealie

```yaml
services:
  mealie:
    image: ghcr.io/mealie-recipes/mealie:v2.5.0 #
    container_name: mealie
    restart: always
    ports:
      - "9999:9000"
    deploy:
      resources:
        limits:
          memory: 1000M
    volumes:
      - mealie-data:/home/pe8er/docker/mealie
    environment:
      ALLOW_SIGNUP: "true"
      PUID: 1000
      PGID: 1000
      TZ: Europe/Warsaw
      BASE_URL: http://localhost:9999
      OPENAI_API_KEY: [OPENAI_API_KEY]

volumes:
  mealie-data:
``` -->

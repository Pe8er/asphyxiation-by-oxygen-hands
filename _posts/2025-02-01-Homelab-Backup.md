---
title: "Automated Raspberry Pi Backup with rsync"
date: 2025-02-01
toc: true
categories:
  - Blog
tags:
  - Homelab
---

A couple of weeks ago, I made [an attempt to reduce USB hard drive noise]({% post_url 2025-01-06-Homelab-HDD-Noise %}) by idling it automatically. It didn't work out that well -- the drive seems to be possessed, turns itself on seemingly randomly, and the logs tell me _nothing_.<!--more--> I figured a better solution would be to mount it only when it's needed, which coincides with my decision to use it just for backups.

This post is a part of my _Homelab Series_. [See the index here]({%- post_url 2025-01-01-Homelab-Introduction -%}).
{: .notice}

## Backup Script

I asked ChatGPT to write the backup script for me and I feel empty. It was just too easy. I missed the dopamine kick from figuring out the arcane arts of shell scripting, ugh. And because I was very lazy at prompting, it took several attempts to make it do exactly what I want. And again, instead of a sense of satisfaction or learning, it felt like a frustrating encounter with a incredibly talented but woefully unimaginative person.

And after that, the universe heard my request for a challenge and for two straight days, I wasn't able to figure out why `rsync` wouldn't accept my exclude lists. I finally figured out a solution but I have _no idea_ why the original approach (using an array with `--exclude=` option) didn't work.

Anyway, here's the script. It stops all docker containers, mounts the external drive, performs 2 backup operations, restarts docker containers, unmounts the drive and sends a report to my phone. It even has a dry run mode -- simply run `sudo backup.sh test` for a test run with more logging and some actions (like unmounting) disabled.

```bash
#!/bin/bash

# Check for root privileges
[[ $EUID -ne 0 ]] && { echo "This script must be run as root."; exit 1; }

# Paths
backupDrive="/dev/sda2"
backupDriveMount="/mnt/media"
backupPath="$backupDriveMount/Backups"
scriptPath="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

# Define backup sources and destinations
declare -a backups=(
  "/home/ home"
  "/mnt/ssd-sandisk/ ssd-sandisk"
)

# Set the locale to avoid issues with rsync output
export LC_ALL=C

# Pushover credentials
tokenUser="u22kk8j5cyb3a9ji7s5yh1mi7hk12n"
tokenApp="a7f44m51kzzgoa4mwtn77q8zb39n3i"

# Log file location
logFile="/var/log/backup.log"

# Check if log file exists
if [ ! -f "$logFile" ]; then
  touch "$logFile"
else
  # Get the current date and the file's modification date in seconds since epoch
  currentTime=$(date +%s)
  fileModificationTime=$(stat -c %Y "$logFile")

  # Calculate the age of the file in seconds
  fileAge=$((currentTime - fileModificationTime))

  # Calculate the number of seconds in 3 months (approximately 90 days)
  threeMonthsInSeconds=$((90 * 24 * 60 * 60))

  # Check if the file is older than 3 months
  if [ $fileAge -gt $threeMonthsInSeconds ]; then
    rm "$logFile"
    touch "$logFile"
  fi
fi

# Check for 'test' argument to enable dry run
dryRunFlag=""
[[ "$1" == "test" ]] && dryRunFlag="--dry-run -P"

# Log messages
logMessage() {
  local message="$1"
  echo "$(date '+%Y/%m/%d %H:%M:%S') - $message" | tee -a "$logFile"
}

# Initialize backup report for Pushover notification
backupReport=""

# Send Pushover notifications
sendNotification() {
  local title="$1"
  local message="$2"
  curl -s \
    --form-string "token=$tokenApp" \
    --form-string "user=$tokenUser" \
    --form-string "title=$title" \
    --form-string "message=$message" \
    https://api.pushover.net/1/messages.json >/dev/null
}


# Perform rsync backup
backup() {
  local source="$1"
  local description="$2"
  local destination="$backupPath/$description/"
  local options=""

  [ -f "$scriptPath/$description-exclude.txt" ] && options="--exclude-from=$scriptPath/$description-exclude.txt"
  
  logMessage "Starting backup of ${description}: ${source} -> ${destination}"
  rsyncOutput=$(rsync -avzh $dryRunFlag --delete --stats ${options:+"$options"} "$source" "$destination" 2>&1)
  if [[ $? -ne 0 ]]; then
    sendNotification "Pi Backup ❌" "Failed to back up ${description}."
    logMessage "Error during backup of ${description}."
    logMessage "$rsyncOutput"
  else
    # Extract the number of regular files transferred
    numFilesTransferred=$(echo "$rsyncOutput" | awk '/Number of regular files transferred/{print $NF}')
    logMessage "Completed backup of ${description}. Files transferred: ${numFilesTransferred}."
    backupReport+="${description}: ${numFilesTransferred} files\n"
  fi
}

# Unmount backup hard drive
unmountMedia() {
  if ! sudo umount $backupDriveMount; then
    logMessage "$backupDriveMount is busy. Attempting lazy unmount."
    if ! sudo umount -l $backupDriveMount; then
      sendNotification "Pi Backup ❌" "Failed to unmount $backupDriveMount."
      logMessage "Lazy unmount of $backupDriveMount failed."
      exit 1
    else
      logMessage "Lazy unmount of $backupDriveMount succeeded."
    fi
  else
    logMessage "Unmounted $backupDriveMount."
  fi
}

#############################################

if [[ "$1" != "test" ]]; then
  # Stop all docker containers
  logMessage "Stopping all docker containers…"
  docker stop $(docker ps -q)
fi

# Mount backup hard drive
if ! mountpoint -q $backupDriveMount; then
  if ! sudo mount $backupDrive $backupDriveMount; then
    sendNotification "Pi Backup ❌" "Failed to mount $backupDrive at $backupDriveMount."
    exit 1
  fi
else
  logMessage "$backupDriveMount is already mounted."
fi

# Ensure the backup directory exists
sudo mkdir -p $backupPath

# Perform backups
for backupItem in "${backups[@]}"; do
  IFS=' ' read -r source description <<< "$backupItem"
  backup "$source" "$description"
done

if [[ "$1" != "test" ]]; then
  # Unmount backup drive
  unmountMedia
  # Restart docker containers
  logMessage "Restarting all docker containers."
  docker start $(docker ps -a -q)
fi

# Send success notification
sendNotification "Pi Backup ✅" "$(printf "$backupReport")"
logMessage "$backupReport"
```

## Excluding Stuff

If you want to exclude directories or files from backup, create a text file in the same directory as the script, which has to be named the same as backup destination with `-exclude` suffix. Backup script will pick it up automatically.

Example time! If you declare your backup like this:

```bash
declare -a backups=(
  "/ root-sd-card"
)
```

Your exclude file needs to be named `root-sd-card-exclude.txt`. Here's how I set up mine:

**SD Card Root (sd-root-exclude.txt):**

```text
*DS_Store
/dev/*
/home/pe8er/.cache/*
/home/pe8er/.vscodium-server/*
/lost+found
/media/*
/mnt/*
/proc/*
/run/*
/sys/*
/tmp/*
/var/log/*
/var/swap
/var/tmp/*
logs*
logs/*
```

**SSD Drive (ssd-sandisk-exclude.txt):**

```text
.caltrash
.stversions
*DS_Store
/Downloads/*
logs*
logs/*
```

## Automate Backup

Thank god for [crontab.guru](https://crontab.guru/). I want to run the script every Saturday at 1 AM and apparently this does it:

```bash
sudo crontab -e
```

```config
0 1 * * 6 /home/pe8er/backup.sh
```

## Don't Forget

To remove backup drive's entry from `/etc/fstab` so that it doesn't mount on boot anymore.

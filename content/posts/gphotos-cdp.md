---
title: "Google Photos Automatic Backup - Original Quality"
subtitle: ""
description: "Gphotos Cdp - did not get this working."
date: 2021-12-29T02:06:06+11:00
lastmod: 2021-12-29T02:06:06+11:00
draft: true

author: "JD"
authorLink: ""

tags: [Homelab,Backup]
categories: [Homelab]


hiddenFromHomePage: false
hiddenFromSearch: false
featuredImage: ""
featuredImagePreview: ""
toc:
    enable: true
    auto: true

comment:
    enable: true

seo:
    images:
---

The previous post offered a backup of the photos in the Google Photos album, but not at the original quality.

Although this method does not use a robust method (not using Google Photos API), I'm running this in parallel to ensure photos are backed up at full quality.

## Documentation
https://github.com/perkeep/gphotos-cdp
https://github.com/JakeWharton/docker-gphotos-sync
https://jakewharton.com/removing-google-as-a-single-point-of-failure/

## Docker Setup

### Create Persistent Volumes
Data directory: `/volume1/docker/gphotos-sync-cdp`
- Download sub-directory `/volume1/docker/gphotos-sync-cdp/downloads`
- Config directory `/volume1/docker/gphotos-sync-cdp/config`

### Authentication
1. Start up a docker container pointing to the config directory.
```bash
docker run -p 6080:80 \
    -v /volume1/docker/gphotos-sync-cdp/config:/config \
    dorowu/ubuntu-desktop-lxde-vnc
```
2. Visit the URL `http://localhost:6080/` or 'http://server-ip:6080/'


3. 

### Initial Sync

```bash
docker run -it --rm \
    -v /volume1/docker/gphotos-sync-cdp/config:/tmp/gphotos-cdp \
    -v /volume1/docker/gphotos-sync-cdp/downloads:/download \
    jakewharton/gphotos-sync \
    /app/sync.sh
```
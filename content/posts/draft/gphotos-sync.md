---
title: "Gphotos Sync"
subtitle: ""
description: "Gphotos Sync"
date: 2021-12-29T00:32:38+11:00
lastmod: 2021-12-29T00:32:38+11:00
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
## Documentation
https://github.com/gilesknap/gphotos-sync

https://bullyrooks.com/index.php/2021/02/02/backing-up-google-photos-to-your-synology-nas/

## Preparation

### Create Persistent Storage
I'll be using the following directories. Don't forget to create them.

`/storage` --> /volume1/docker/gphotos-sync/storage
`/config` --> /volume1/docker/gphotos-sync/config

### client_id and client_secret
To obtain the client secret, go to the [Google Photos API](https://developers.google.com/photos/library/guides/get-started) and click on "Enable the Google Photos API".

Create a project and product name.

For "Configure your OAuth client" select "Desktop application" and click "Create".

Download the client secret, rename it as client_secret.json and add it to the `config` directory.

## Docker Command

```bash
docker run \
   -ti \
   --name gphotos-sync \
   -v /volume1/docker/gphotos-sync/storage:/storage \
   -v /volume1/docker/gphotos-sync/config:/config \
   gilesknap/gphotos-sync \
  /storage
```

Copy the link the first time and paste it into a browser. Allow both permissions (manage and view)

Copy the token and paste in the docker container.

## Run the backup
After the initial setup, a backup will be performed.

### Automating the backup (Synology NAS)

Follow [here](https://bullyrooks.com/index.php/2021/02/02/backing-up-google-photos-to-your-synology-nas/)
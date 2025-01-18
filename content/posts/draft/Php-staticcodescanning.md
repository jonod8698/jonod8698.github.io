---
title: "Php Staticcodescanning"
subtitle: ""
description: "Php Staticcodescanning"
date: 2022-03-17T00:46:16+11:00
lastmod: 2022-03-17T00:46:16+11:00
draft: true

author: "JD"
authorLink: ""

tags: []
categories: []


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

## Exakat
```
docker run -v C:\Users\user\exakat:/usr/src/exakat/projects --rm exakat/exakat exakat init -p contoso-php
docker run -v C:\Users\user\exakat:/usr/src/exakat/projects --rm exakat/exakat exakat project -p contoso-php
```

# Phpstan
Link docker image to local windows system folder. Execute in local windows system powershell.
```
docker pull ghcr.io/phpstan/phpstan
docker run --rm -v C:\Users\user\phpstan\code:/app ghcr.io/phpstan/phpstan analyse bin/
```
---
title: "Stormspotter"
subtitle: ""
description: "Stormspotter"
date: 2022-01-25T11:15:22+11:00
lastmod: 2022-01-25T11:15:22+11:00
draft: false

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

## Introduction

[Stomspotter](https://github.com/Azure/Stormspotter) is an Azure Red Team tool to graph Azure and Azure AD objects.

By mapping out relationships between objects, Stormspotter visualises attack paths between Azure objects.

Stormspotter currently only [supports Neo4j 3.x.x](https://github.com/Azure/Stormspotter/issues/49).

I recommend setting `image: neo4j:3.5.18` in your docker-compose.yml


## Requirements
- Docker
- Docker Compose
- Python 3.8.X
- Az PowerShell

## Installation

Installation via Docker to avoid manual installation of dependencies.

The docker-compose file creates three containers:
- Frontend
- Backend
- Neo4j

```powershell
git clone https://github.com/Azure/Stormspotter
cd Stormspotter
docker-compose up -d
```

Stormspotter Frontend will expose a WebUI on port 9091.

Neo4j will be exposed on port 7474 (HTTP) amd 7687 (Bolt).

Default credentials are specified in the docker-compose file. These should be changed. 

![Docker-compose password](/2022-01-25-11-23-46.png)

## Execution

### Stormcollector

1. Download the relevant package from [Stormspotter Releases](https://github.com/Azure/Stormspotter/releases/)
2. Login via `az login --allow-no-subscriptions`
3. Execute the stormcollector pyz file to collect Azure objects.

```powershell
cd stormcollector
python3 sscollector.pyz cli
```

4. Locate the output in the stormcollector folder with the pyz file.
![Stormspotter-output](/stormspotter-output.png)

5. Upload to StormCollector UI

6. Check logs in backend to see processing status
![Processing-status](/2022-01-25-12-10-44.png)

7. Complete. Enjoy!


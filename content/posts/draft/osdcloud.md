---
title: "OSDCloud"
subtitle: ""
description: "Osdcloud"
date: 2022-01-24T22:52:11+11:00
lastmod: 2022-01-24T22:52:11+11:00
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

## Introduction

OSDCloud is something I've always seen recommended by various people I follow on Twitter. It's a way to deploy a Windows 10 Image to a bare-metal device without any additional infrastructure.

Additional Features include:
- 

## Requirements

https://www.osdeploy.com/blog/2021/osdcloud#requirements

- Windows 10 x64
- Windows ADK
    - Deployment
    - WinPE (Separate installer)

## OSDCloud Setup

```powershell
install-module osd -force
```

Check Version of OSD
```powershell
Get-InstalledModule -Name OSD
```

Get OSDHelp
```powershell
OSDHelp OSDCloudSetup
```

Navigate to OSDCloudSetup/Quick Setup.ps1

![](/osdcloud-quicksetup.png)

Change as required. I added Lenovo and HP instead of Dell.

Click Run

### Additional Information

- [OSDCloud Youtube Talk](https://www.youtube.com/watch?v=ltCRnl8MCjI)
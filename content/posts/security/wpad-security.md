---
title: "WPAD Security Considerations"
subtitle: ""
description: "Wpad Security"
date: 2022-03-20T16:29:31+11:00
lastmod: 2022-03-20T16:29:31+11:00
draft: false

author: "JD"
authorLink: ""

tags: [Network]
categories: [Security]


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

## What is WPAD?

WPAD stands for Windows Proxy Auto-Discovery. It is a protocol to obtain the URL for a PAC file.

WPAD uses the following methods in order to discover the PAC file URL:
- DHCP
- DNS
- WINS
- LLMNR
- NetBIOS
- Hosts
- Lmhosts

The PAC file tells the host where to direct network traffic. This is usually used to force traffic through a web proxy.

## Why is it insecure?

When DHCP or DNS is misconfigured or not configured, an attacker can provide the client with a PAC file that directs traffic to a compromised server.

## Mitigations

There is no one-size fits all solution, but in general there are two cases:

1. If a web filter with an alternative PAC delivery mechanism is installed or a web filter is not used:
    - [Disable WPAD client side](https://kc.mcafee.com/corporate/index?page=content&id=KB90075&locale=en_US).
    - Sinkhole wpad.domain.com via DNS.
    - Enable DHCP Guarding and/or set DHCP option 252 to sinkhole WPAD requests.
    - Configure firewalls to block outbound requests for wpad.dat files.

2. If a web filter is installed and configured to deliver PAC files:
    - Ensure DHCP option 252 is set to the correct value.
    - Enable DHCP Guarding to defend against rogue DHCP servers.
    - Ensure a DNS entry is created for wpad.domain.com, pointing to the server with a PAC file.
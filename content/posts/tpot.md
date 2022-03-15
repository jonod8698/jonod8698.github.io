---
title: "Creating a Honeypot with T-Pot"
subtitle: ""
description: "Tpot"
date: 2022-01-16T13:56:28+11:00
lastmod: 2022-01-16T13:56:28+11:00
draft: false

author: "JD"
authorLink: ""

tags: [Honeypot]
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

## Summary

[Tpotce](https://github.com/telekom-security/tpotce) is an all-in-one honeypot platform with a collection of honeypots and tools for monitoring them.

## Requirements

- 8GB RAM
- 128GB Disk Space
- Unfiltered Internet Access
- Isolated Subnet
- Promiscious Mode Enabled for fatt, suricata and p0f to work properly
- Port forward or NAT to the honeypot

## Installation

Set up is simple as the ISO is prebuilt.

1. Download the latest ISO from [Tpotce releases](https://github.com/telekom-security/tpotce/releases)
2. Mount the ISO to a VM and run through the installer.
3. Select "Standard" as an install option.
4. The install should take around 30 minutes.

## Network Configuration

### Isolated Subnet

To prevent attackers from accessing your internal network via the honeypot, you need to create an isolated subnet.

In Unifi-land you can create an isolated subnet by:
a) setting a different VLAN ID for your isolated network. (Layer 2)
b) Enabling **Device Isolation** which effectively marks the network as a Guest Network and applies **Guest Rules**. (Layer 3/4)

Based on my testing, built-in Guest Rules perform the following:

- LAN to Guest Allowed
- Guest to LAN not Allowed
- Stateful traffic not Allowed
- DNS requests to anywhere Allowed

![Unifi Network Settings](/tpot-network-settings.png)

Unifi Firewalls have 3 types of networks (Internet,LAN,Guest) with 3 types of rules (In, Out, Local).

#### Types of Networks

Internet is simple enough. Anything not RFC1918 is considered an Internet network.

LAN is any network without Device Isolation enabled.

Guest is any network with Device Isolation enabled.

#### Types of Rules

This part is a bit tricky as you need to think from the firewall's perspective.

Let's consider Guest IN rules. These match traffic from the Guest Network **IN to** the firewall, then to the LAN.

Guest OUT is the opposite, any traffic from the firewall **OUT to** the Guest Network. So LAN to Guest traffic would match Guest OUT rules.

Guest LOCAL is more simple. It's the router interface, i.e. the default gateway. Here instead of allowing default rules which include DNS, ICMP, and RADIUS, I only allow DHCP.

### Port Forwarding

Ports 1-64000 should be forwarded to tpot.

If you need to access the Web UI externally, you can forward ports 64297 and 64294.

### Port Mirroring (optional)

I use vSphere 6.7U3 with a VDS and use [Security Onion 2.3](https://securityonionsolutions.com/). To mirror traffic to a monitoring interface:
1. Right click the VDS > Configure > Port Mirroring
2. New > Distributed Port Mirroring > Next > Status = Enabled > Set Sampling rate if required > Next.
3. Select the Source Interface > Next
4. Select the Destination Interface > Next
5. Finish

## Post-Installation

Visit the T-Pot WebUI at *https://<IP/FQDN>:64294* and enjoy.

## Troubleshooting

I had some routing issues from the TPOT host to my LAN as my internal network uses 192.168.0.0/16 IP range. The route for 192.168.0.0/20 created by TPOT prevented traffic from returning to my LAN so I created another /32 static route for my computer.

I think it would be good if TPOT excludes the 192.168.1.0/24 subnet.

![Routing Issue](/tpot-route.png)
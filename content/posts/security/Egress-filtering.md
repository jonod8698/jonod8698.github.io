---
title: "Egress Filtering"
subtitle: ""
description: "Egress Filtering"
date: 2022-09-18T21:12:58+10:00
lastmod: 2022-09-18T21:12:58+10:00
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
## Summary
Egress filtering determines what traffic leaves the network perimeter. 

Why? Egress filtering is good security architecture as it prevents computers on the network from sending unwanted traffic to the internet. Limiting the attack surface protects against low-skill opportunistic attacks while reducing the breath of things to monitor.
- C2 communication
- Exposing information to attackers

Network traffic Analysis solutions such as [Elastiflow](https://www.elastiflow.com/) can provide data on ports/IPs being used.

## What to Block?
A deny by default, allowlisting approach is ideal. Denylisting first is a "safer" transitory step.

Here are some recommended ports to block:

- 135 - MSRPC
- 137-139 - NetBIOS/IP
- 445 - SMB/IP
- 69 - TFTP
- 514 - Syslog
- 3389 - RDP
- 161-162 - SNMP
- 25 - SMTP
- 6660-6669 - IRC
- 123 UDP - NTP
- 21 - FTP
- 22 - SSH
- 23 - Telnet
- 515 - LDP Printing
- 9100 - Printing
- 6881-6889 - BitTorrent
- 6969 - BitTorrent
- ICMP Echo-Replies (type 0 code 0)*
- ICMP Host Unreachables (type 3 code 1)*
- ICMP Time Exceeded in Transit (type 11 code 0)*

*Try to block ports as well as Protocols.

## How to verify egress filtering?
Do an nmap scan to allports.exposed from [BHIS](https://www.blackhillsinfosec.com/poking-holes-in-the-firewall-egress-testing-with-allports-exposed/)  or run the following script

```PowerShell
1..1024 | % {$test= new-object system.Net.Sockets.TcpClient; $wait = $test.beginConnect("allports.exposed",$_,$null,$null); ($wait.asyncwaithandle.waitone(250,$false)); if($test.Connected){echo "$_ open"}else{echo "$_ closed"}} | select-string " "
```

Note: ISPs typically filter a TON of ports such as port 25.

## Resources
https://sansorg.egnyte.com/dl/fyF6fGJ4lz
https://insights.sei.cmu.edu/blog/best-practices-and-considerations-in-egress-filtering/
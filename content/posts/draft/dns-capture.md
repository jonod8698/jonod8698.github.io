---
title: "Dns Capture"
subtitle: ""
description: "Dns Capture"
date: 2020-08-02T13:57:51+10:00
lastmod: 2020-08-02T13:57:51+10:00
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

Sometimes you need to do a packet capture on a Windows machine without installing anything. This is a guide on how to use the inbuilt Windows utility – netsh trace – to capture IPv4 packets. For just capturing DNS packets, capturing only UDP packets should cover most DNS traffic.

```powershell
#Run this command in an elevated command prompt
netsh trace start maxSize=500 capture=yes Ethernet.Type=IPv4 Protocol=UDP report=disabled

#When you want to stop the packet capture run
netsh trace stop
```
For capturing all types of packets, leave out the Protocol Type:

```powershell
netsh trace start maxSize=500 capture=yes Ethernet.Type=IPv4 report=disabled
```
![](/images/2022-08-02-13-59-04.png)

Next, download etl2pcapng from Github then run the following commands to convert the .etl file to a PCAPNG.

```powershell
#https://github.com/microsoft/etl2pcapng
#Convert ETL file to pcapng
.etl2pcapng.exe .NetTrace.etl nettrace.pcapng
```

Open the .PCAPNG file just created in Wireshark and go to File > Save As > PCAP

![](/images/2022-08-02-13-59-34.png)

You now have a PCAP file that can be imported into Zeek for analysis in RITA
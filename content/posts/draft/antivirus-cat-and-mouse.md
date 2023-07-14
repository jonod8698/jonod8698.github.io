---
title: "Antivirus Cat and Mouse"
subtitle: ""
description: "Antivirus Cat and Mouse"
date: 2022-07-27T21:53:21+10:00
lastmod: 2022-07-27T21:53:21+10:00
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

## Title 1

### Subtitle 1

## Title 2

### Subtitle 2

## Cat and Mouse Game
Tic-tac-toe is a solved game as it has a finite number of possibilies. Malware on the other hand have an essentially infinite number of possible permutations. We must accept that AV will never achieve 100% detection against all possible attacks.

A typical adversary's malware development cycle might be: Create malware --> test against offline AV used by target company --> if detected, try again until undetected --> distribute.

A typical detection engineer's development cycle might be: Receive details from DFIR case --> create as robust a detection as possible while minimising false positives --> distribute via AV updates.

Holding all else equal, the AV vendor with the most installs and therefore the most telemetry should be able to create the best detections. This is unquestionably Microsoft in a Windows environment as Windows Defender is installed by default with Cloud delivered protection.

But this is a double edged sword, Microsoft Defender is easily accessible for testing malware detection and most malware produced will ensure it is not detected by it.

AV availability, iteration, first try undetected.

Offline scanning. Double edged sword. Means adversaries can't bypass.
https://www.digitaltrends.com/computing/microsoft-defender-offline-scan-weakness-rivals-dont/

Malware as a Service.
---
title: "Using Hashcat Gpu"
subtitle: ""
description: "Using Hashcat Gpu"
date: 2022-01-22T13:48:42+11:00
lastmod: 2022-01-22T13:48:42+11:00
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

### Any 7 character passwords

```

```

### Numbers

```
.\hashcat.exe -m 1000 -a 3 .\hashes-IT.txt ?d?d?d?d?d?d?d?d?d?d?d?d --increment -o .\cracked-it.txt -O
```

### All 1-8 character passwords with lowercase and uppercase alphanumerics

```powershell
.\hashcat.exe -m 1000 hashes.txt -a 3 ?1?1?1?1?1?1?1?1 --increment -1 ?l?d?u -O
```

```
hashcat -m 1000 -a 3 hashes\users.txt ?u?l?l?l?s?d?d?d?d
hashcat -m 1000 -a 3 hashes\users.txt ?u?l?l?l?l?s?d?d?d?d
hashcat -m 1000 -a 3 hashes\users.txt ?u?l?l?l?l?s?d?d
hashcat -m 1000 -a 3 hashes\users.txt ?u?l?l?l?s?d?d?d
hashcat -m 1000 -a 3 hashes\users.txt -1 ?u?l ?1?1?1?s?d?d?d?d
hashcat -m 1000 -a 3 hashes\users.txt -1 ?u?l ?1?1?1?1?s?d?d?d?d

hashcat -m 1000 -a 3 hashes\users.txt ?u?l?l?l?l?d?d?d?d
hashcat -m 1000 -a 3 hashes\users.txt ?u?l?l?l?l?l?d?d?d
hashcat -m 1000 -a 3 hashes\users.txt ?u?l?l?l?d?d?d?d
hashcat -m 1000 -a 3 hashes\users.txt ?u?l?l?l?l?d?d?d
hashcat -m 1000 -a 3 hashes\users.txt ?u?l?l?l?l?l?d?d
hashcat -m 1000 -a 3 hashes\users.txt ?u?l?l?l?l?l?l?d
hashcat -m 1000 -a 3 -1 ?u?l hashes\users.txt ?1?1?1?1?1?1?d?s
hashcat -m 1000 -a 3 -1 ?u?l hashes\users.txt ?1?1?1?l?d?d?d?d
hashcat -m 1000 -a 3 -1 ?u?l hashes\users.txt ?1?1?1?d?d?d?d?s
hashcat -m 1000 -a 3 -1 ?u?l hashes\users.txt ?1?1?1?d?d?d?d?s
hashcat -m 1000 -a 3 -1 ?u?l hashes\users.txt ?1?1?1?l?d?d?d?d?s
hashcat -m 1000 -a 3 -1 ?u?l hashes\users.txt ?1?1?1?l?d?d?s?s
hashcat -m 1000 -a 3 -1 ?u?l hashes\users.txt ?1?1?1?1?d?d?d?s

.\hashcat.exe -m 1000 -a 0 .\hashes.txt .\dicts\cyclone.hashesorg.hashkiller.combined.txt -r .\rules\OneRuleToRuleThemAll.rule -O

.\hashcat.exe -m 1000 -a 0 .\hashes.txt .\dicts\cyclone.hashesorg.hashkiller.combined.txt -r .\rules\dive.rule -O
```

https://weakpass.com/download
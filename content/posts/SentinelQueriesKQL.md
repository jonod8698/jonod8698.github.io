---
title: "SentinelQueriesKQL"
subtitle: ""
description: "SentinelQueriesKQL"
date: 2022-03-15T19:35:07+11:00
lastmod: 2022-03-15T19:35:07+11:00
draft: true

author: "JD"
authorLink: ""

tags: [KQL]
categories: [Sentinel]


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

## Identity Queries

### Detect Users in an OU not in an Azure AD Group
Let's say you need to find out which AD users are in an OU but not in an Azure AD group. You can use the following query to find the users in the OU but not in the group:

```kql
IdentityInfo
| where OnPremisesDistinguishedName endswith_cs "OU=COMPANY,OU=STORES,OU=ICSH,DC=ICSH,DC=LOCAL"
| where GroupMembership !contains "Company Store Users"
```

### Detect Users using Office Resources on Personal Devices
Identify users logging in from non company devices. Define company device as Azure AD joined or Hybrid AD joined.



## Endpoint

### Subtitle 2
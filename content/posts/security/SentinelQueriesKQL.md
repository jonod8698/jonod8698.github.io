---
title: "SentinelQueriesKQL"
subtitle: ""
description: "SentinelQueriesKQL"
date: 2022-03-15T19:35:07+11:00
lastmod: 2022-03-15T19:35:07+11:00
draft: false

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

```
union AADNonInteractiveUserSignInLogs,SigninLogs
| where Identity !startswith_cs "Franchise Store"
| where AppDisplayName != "Outlook Mobile"
| where IPAddress !in ("101.97.60.198","183.177.50.50") // equinix new ip
| where DeviceDetail_string !contains "Azure AD"
| where DeviceDetail_dynamic !contains "Azure AD"
| where DeviceDetail_string !contains_cs "Ios"
| where DeviceDetail_string !contains "Android"
| where DeviceDetail_string !contains "MacOs"
| where NetworkLocationDetails !contains_cs "Zscaler"
| summarize count() by Identity,IPAddress,Location,DeviceDetail_string
```

### MFA prompt vs satisfied

```
//Visualize when your users are actively challenged for MFA vs when it was previously satisfied
SigninLogs
| where TimeGenerated > ago(365d)
| where AuthenticationRequirement == "multiFactorAuthentication"
| extend x=todynamic(AuthenticationDetails)
| mv-expand x
| project TimeGenerated, x
| extend MFAResultStep = tostring(x.authenticationStepResultDetail)
| summarize
    MFARequired=countif(MFAResultStep == "MFA completed in Azure AD"),
    PreviouslySatisfied=countif(MFAResultStep == "MFA requirement satisfied by claim in the token")
    by bin(TimeGenerated, 1d)
| render timechart
    with (
    xtitle="Day",
    ytitle="Count",
    title="MFA challenges vs MFA previously satisfied over time")
```

### Apps with no signins in last month
Can be used for cleanup or detect apps bypassing SSO

```
//Find Azure AD applications that have had no signins for over 30 days. May be a sign of an app no longer in use or users bypassing SSO.
SigninLogs
| where TimeGenerated > ago (365d)
| where ResultType == 0
| summarize arg_max(TimeGenerated, *) by AppId
| project
    AppDisplayName,
    ['Last Logon Time']=TimeGenerated,
    ['Days Since Last Logon']=datetime_diff("day", now(), TimeGenerated)
| where ['Days Since Last Logon'] > 30
```

## Endpoint

### Devices without contact

```
//Find devices that have stopped sending network events over the last 30 days, retrieve last event time and calculate the days since last event
let timerange=365d;
let timeframe=30d;
DeviceNetworkEvents
| project TimeGenerated, DeviceName
| where TimeGenerated > ago(timerange)
| summarize arg_max(TimeGenerated, DeviceName) by DeviceName
| join kind=leftanti (
    DeviceNetworkEvents
    | project TimeGenerated, DeviceName
    | where TimeGenerated > ago(timeframe)
    | summarize arg_max(TimeGenerated, DeviceName) by DeviceName)
    on DeviceName
| extend DaysSinceLastEvent = datetime_diff('day', now(), TimeGenerated)
| project DeviceName, DaysSinceLastEvent, LastEventTime=TimeGenerated
```
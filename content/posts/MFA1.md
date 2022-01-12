---
title: "Multi-Factor Authentication via Conditional Access"
subtitle: ""
description: "MFA1"
date: 2022-01-12T01:54:05+11:00
lastmod: 2022-01-12T01:54:05+11:00
draft: false

author: "JD"
authorLink: ""

tags: [AzureAD, Conditional Access, MFA]
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

## 1. Introduction

If your users have Azure AD P1, Microsoft strongly recommends switching from Per-User MFA to Condtional Access for several reasons:
- Improved User Experience
- Granular Access Control
- Delegated Control to Helpdesk

## 2. Why use Conditional Access to apply MFA? 

### 2.1 Improved User Experience
Conditional Access allows for less MFA prompts

https://dirteam.com/sander/2020/06/17/todo-move-from-the-allow-users-to-remember-multi-factor-authentication-on-devices-they-trust-option-to-conditional-access/

https://docs.microsoft.com/en-us/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime


### 2.2 Granular Access Control

Per-User MFA can be turned on or off with no granularity outside of IP range exclusions. 

Enabling MFA via CA allows for granular control of MFA prompts based on Risk (P2 license), Device status, application, and [more](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/overview).

### 2.3 Delegated Control to Helpdesk

Per-User MFA is the legacy PhoneFactor portal. It does not allow for Azure AD RBAC delegation to [control MFA through the GUI](https://docs.microsoft.com/en-us/azure/active-directory/roles/permissions-reference#authentication-administrator).

## 3. Test Scenarios

1. Per User MFA and CA policy applied

2. Per User MFA
    - switch from Enforced to Enabled.
    - switch from Enabled to Disabled.

3. Conditional Access MFA 

4. Self-Service Password Reset Behaviour for end users.

5. New User Force MFA

6. Force Password Change

## 4. Strategy

- Corporate owned Windows 10 devices: Enrolled in Intune and compliant

- Personal owned smartphones (BYOD): Must use an approved client app because MAM policies are deployed with Intune

- Web browser access from unmanaged devices: MFA is enforced, additionally app enforced restrictions prevent file downloads

- Guest users are not affected by any rules whereas files shared with them are monitored with cloud app security


## 5. Scripts

### 5.1 Per User MFA Enable/Disable

[Install Instructions](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)

```powershell
#Requires -Modules MSOnline
Connect-MsolService

Get-MsolUser | Select-Object UserPrincipalName,StrongAuthenticationMethods,StrongAuthenticationRequirements | Format-Table -AutoSize
```

Links
https://www.adaxes.com/script-repository/status-of-users-mfa-in-microsoft-365-s601.htm

https://www.adaxes.com/script-repository/check-multi-factor-authentication-status-for-a-user-in-office-365-s556.htm

https://www.adaxes.com/script-repository/enabledisable-multi-factor-authentication-for-a-user-in-office-365-s544.htm

### 5.2 Conditional Access MFA

### 5.3 Modify Authentication Methods

#### 5.3.1 Re-register MFA

#### 5.3.2 Revoke MFA

#### 5.3.3 Add Phone Number

### 5.4 Module to export MFA status to CSV

See [LazyAdmin](https://lazyadmin.nl/powershell/list-office365-mfa-status-powershell/)


#### 5.4.1 Export MFA status to CSV
```powershell
#Requires -Modules MSOnline
Connect-MsolService
.\MFAStatus.ps1  | Export-CSV c:\temp\UserMFAStatus.csv -noTypeInformation
```

#### 5.4.3 Filter for users by attributes 

```powershell
#Requires -Modules MSOnline
Connect-MsolService


## FAQ

### Can Per-user MFA and Conditional MFA be used together?

### Converting from Per-User MFA to Conditional Access MFA
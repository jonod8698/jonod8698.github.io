---
title: "Domainjoin"
subtitle: ""
description: "Domainjoin"
date: 2022-01-14T12:36:18+11:00
lastmod: 2022-01-14T12:36:18+11:00
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

```powershell
Write-Host "Please enter your desired computer name: [Default $env:computername]:"
$computername = Read-Host

$renamecomputer = $true
if ($computername -eq "" -or $computername -eq $env:computername) { $computername = $env:computername; $renamecomputer = $false }

$ou = "OU=Computers,OU=Domain Controllers,DC=domain,DC=local"

$credentials = New-Object System.Management.Automation.PsCredential("yourdomain\useraccountwithjoinpermissions", (ConvertTo-SecureString "useraccountpassword" -AsPlainText -Force))
Write-Host "Adding $computername to the domain"
Add-Computer -DomainName "your.domain.here" -Credential $credentials -OUPath $ou
if ($renamecomputer -eq $true) { Rename-Computer -NewName $computername -DomainCredential $credentials -Force }
Restart-Computer
```
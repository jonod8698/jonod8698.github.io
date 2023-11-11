---
title: "Remote Credential Guard"
subtitle: ""
description: "Remote Credential Guard"
date: 2022-03-19T00:27:04+11:00
lastmod: 2022-03-19T00:27:04+11:00
draft: false

author: "JD"
authorLink: ""

tags: [Remote Credential Guard,Kerberos]
categories: ['Security']


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

Remote Credential Guard (RCG) protects admin credentials by ensuring they are not passed over the network to a target device. RCG is particularly useful for administrators who need to remotely access and manage servers and other critical systems. By ensuring that credentials are not exposed during these remote sessions, it significantly reduces the risk of credential theft and lateral movement in the network.

## Details

Clients with RCG enabled do not send primary credentials to the target machine. This mitigates the risk of stolen credentials if the target is compromised.

In RCG, the client LSA acts as a reverse proxy for ticket requests from the target. Hence, the target never needs to store primary credentials.

RCG will not allow NTLM fallback.

The flow goes something like this (very simplified):

1. App on target (server) needs a ticket so it asks LSA.
2. LSA opens a channel back to the client LSA.
3. Target LSA asks client LSA to ask for a ticket.
4. Client LSA forwards ticket to Target LSA.

## Limitations

- Native RDP client only uses logged in user credentials. Devolutions Remote Desktop Manager can use saved credentials.
- While TGT is encrypted, service tickets are not. If an RDP sesion is initiated to a compromised target or an attacker comrpomises the target while an admin is logged in, the attacker could use the open channel to create sessions on the user's behalf.
- "Windows Defender Remote Credential Guard can be used only when connecting to a device that is joined to a Windows Server Active Directory domain, including AD domain-joined servers that run as Azure virtual machines (VMs). Windows Defender Remote Credential Guard cannot be used when connecting to remote devices joined to Azure Active Directory."
- "Windows Defender Remote Credential Guard does not support compound authentication. For example, if you’re trying to access a file server from a remote host that requires a device claim, access will be denied."
- "Remote Desktop Credential Guard only works with the RDP protocol."

## Implementation

### Requirements

Client must:

- run Windows 10 or Server 2016+
- use Kerberos authentication to connect to the target.

Server must:

- run Windows 10 or Server 2016+
- Allow Restricted Admin connections
- Allow Remote Desktop Connections for the user.
- Allow delegation of non-exportable credentials. (using derivative credentials on a device other than the one it was produced on)

### Instructions

Client

- Ensure the client uses RCG when initating an RDP session:
  - via GPO:
    - From the Group Policy Management Console, go to Computer Configuration -> Administrative Templates -> System -> Credentials Delegation.
    - Set policy to one of the following depending on requirements:
      - Restricted Admin mode or RCG: Choose `Restrict Credential Delegation`
      - Only RCG: Choose `Require Remote Credential Guard`
      - Only Restricted Admin mode: Choose `Require Restricted Admin`
  - with a parameter
    - `mstsc.exe /remoteGuard`

Target Server

- Set the following `reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa /v DisableRestrictedAdmin /d 0 /t REG_DWORD`

## Links

- [Windows Defender Remote Credential Guard - Microsoft Docs](https://docs.microsoft.com/en-us/windows/security/identity-protection/remote-credential-guard)
- [How Authentication Works when you use Remote Desktop - Steve Syfus](https://syfuhs.net/how-authentication-works-when-you-use-remote-desktop)
- [“No more Pass-the-Hash” – Exploring the limitations of Remote Credential Guard - Cyber Ark)](https://www.cyberark.com/resources/blog/no-more-pass-the-hash-exploring-the-limitations-of-remote-credential-guard)
- [Implement RCG LAB - Github Microsoft](https://github.com/MicrosoftLearning/40554A-Microsoft-Security-Workshop-Implementing-Windows-10-Security-Features/blob/master/Instructions/40554A_LAB_05.md)

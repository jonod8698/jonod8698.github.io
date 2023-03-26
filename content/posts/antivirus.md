---
title: "Antivirus is Bad"
subtitle: ""
description: "Antivirus"
date: 2022-07-27T19:44:50+10:00
lastmod: 2022-07-27T19:44:50+10:00
draft: true

author: "JD"
authorLink: ""

tags: [AV]
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

What is Antivirus/AV/NGAV? Is AV "useless"? What AV should I use?

I've been asked all of the above by non-IT and IT users. Usually they fall into two groups. Either AV is useless or AV is the only security required. As usual, the truth is more nuanced. AV is an often unappreciated foundational security control for endpoints.
## What is the purpose of AV?
AV is a scalable platform to block known attack methods/artifacts that increases the cost of attacking and persisting on an endpoint. It exists as untrusted code is allowed to execute by default in major operating systems.

Let's unpack that.
### 1. **Scalable platform.** 
AV companies have teams of detection engineers and security researchers that create detections for known threats using IOCs and TTPs (more robust). It provides widespread protection like a vaccine does against a virus.
![](/images/2022-07-27-22-29-28.png)

### 2. **Block known attack methods/artifacts.**
Known IOCs (file hashes, IP addresses, command lines, process trees, strings) are detected and blocked. Two types of analysis usually occurs:
    - Static Analysis: File properties and contents are analysed without execution. Changing program strings and switching 
    - Dynamic Analysis: Analysis of malware behaviour inside a local/cloud sandbox.

### 3. **Increasing cost for adversaries**.
The cost of producing and maintaining an offensive security operation is significantly increased by the use of AV.

First, decent AV blocks most well known attack methods. Fewer options means more research time and less accessible methods. 95% of kids who Google "how to hack my friends computer" probably won't find any useful methods.

AV also blocks known IOCs so adversaries cannot reuse the same executable and infrastructure. Another increased cost.

Finally, better AV companies are blocking taking a broader view to detection. Instead of using specific hashes/IP addresses/heuristics, AVs are detecting known techniques and procedures of adversaries which is significantly harder for the adversaries to modify. Changing hashes, obfuscation won't work. For example, [Microsoft recently created a detection for legacy process creation](https://www.microsoft.com/security/blog/2022/06/30/using-process-creation-properties-to-catch-evasion-techniques/). An attacker previously using this method to evade Microsoft Defender, has to invest significant time into researching another evasion technique.

![laser beam](/images/2022-07-27-22-22-38.png)

## What does AV not do?
AV only defends against the actions of executables on the endpoint:

- It does not understand good and bad API calls to your webserver.
- It does not know that your printer isn't supposed to be initiaing connections to your computer.
- It does not protect against credential attacks from phishing / hacked website database with password reuse.

## Conclusion
Saying AV is bad because a small proportional of users are infected with malware is like saying vaccines are useless because they don't protect against novel pathogens.

AV serves an important, but specific role in a defense in depth strategy. Endpoint protection will only become more important in a "Zero Trust" world where trust is shifted from the internal network to endpoints.
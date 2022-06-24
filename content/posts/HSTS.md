---
title: "HSTS and Preloading"
subtitle: ""
description: ""
date: 2022-06-24T21:36:19+10:00
lastmod: 2022-06-24T21:36:19+10:00
draft: false

author: "JD"
authorLink: ""

tags: [Security,Web,Network]
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

[HSTS](https://www.chromium.org/hsts/) directs the browser to use HTTPS for certain domains (optionally subdomains). This mitigates some MitM attacks by upgrading HTTP connections to HTTPS.

## HSTS Preloading 
The native implementation of HSTS communicates the HSTS policy to the browser via an HTTP response header `Strict-Transport-Security: max-age=31536000; includeSubDomains`. HSTS requires a user to visit a website once before HSTS protection is cached.

But what if HTTP is used on the first visit or a user clears their cache frequently? An attacker can still redirect the user to a malicious site by altering the response.

Enter [HSTS Preloading](https://hstspreload.org/). It ensures common domains are configured with HSTS without requiring an initial vulnerable visit.

> Another pseudo-HSTS preloading method which works for sites not on the preload list is by [enforcing HTTPS only connections in your browser](https://beebom.com/how-enable-https-only-mode-chrome-firefox-edge-safari/).

## HSTS Preloading Implementation - DevTools
Let's see how HSTS Preloading works in practice.

1. Visiting `http://www.google.com/`

The browser appears to send a request to an internal service to upgrade the HTTP request.
![Chrome](/chrome_WTwn1F0gEw.png)

No HTTP requests hit the network interface. Confirming this is internal to the browser.
![Wireshark](/Wireshark_JgWebww8WD.png)

2. Visiting `http://google.com/`

`http://google.com/` redirects to `http://www.google.com` The request and redirect response hit the network interface in the Wireshark capture below.

Similar to above, `http://www.google.com/` is upgraded to `https://www.google.com/` internally within the browser.

![HSTS](/hsts-http.png)

![Wireshark](/Wireshark_OFBvtEFMto.png)

> If you need to visit Google manually, you should probably type `www.google.com` rather than `google.com`. Address bar searches use HTTPS by default.


### Conclusion

HSTS is a powerful mechanism for protecting users against MitM attacks such as SSL Stripping or Session Hijacking.

But HSTS isn't a simple on/off switch on the client side. Many vulnerable combinations of settings exist, depending on the server and user input.
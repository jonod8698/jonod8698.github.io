---
title: "File Inclusion"
subtitle: ""
description: "File Inclusion"
date: 2022-09-15T22:26:29+10:00
lastmod: 2022-09-15T22:26:29+10:00
draft: true

author: "JD"
authorLink: ""

tags: [AppSec]
categories: [Securtiy]


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

LFI and RFI are terms I've heard many times in the past 5 years, mainly in the context of Windows vulnerabilities and pentesting. I've done CTF challenges with file uploads, then using LFI to execute the shell. I've loaded code from remote sites exploiting RFI vulnerabilities. But I never really associated the terms LFI and RFI with these actions. Perhaps it's because I've spent too long in infrastructure. Anyways, I hope to understand the what file inclusion is, what causes it, and how to fix it.

# Remote File Inclusion (RFI)

RFI occurs when an attacker triggers a webserver to load a remote file.

![](/images/2022-09-15-23-47-34.png)

Impact: attacker can execute code a remote file they control, obtaining a shell on the webserver.

Cause: user directly controlling the file loaded by a server. for example, a navigation page which loads the contents of a file.

So breaking that down.

1. user controlled input.

2. file being loaded by a server using that input e.g. using the `includes` or `requires` function in php

3. lack of input sanitisation.

## How to identify?

-  Look for scripts which take filenames as parameters. Check some common system files & directories.

- For RFI specifically, check PHP config file for `allow_url_fopen` and `allow_url_include` both set to `on`

With access to the source code, check for unvalidated user input.
```php
<?php
$file = $_GET[ 'page' ];
?>
```

## How to fix?
- Quick fix: in the `php.ini` config file, set both  `allow_url_fopen` and `allow_url_include` to `off`.
-   Design stage: prevent user submitted input from being passed to any functions that access the file system.
-   Otherwise, sanitise user input. 
	- Only allow accessing specific files. A simple: if file isn't a.php and b.php etc. don't load the page.
	- access using a long randomised id with the file path stored in a database.

## Bypass defences
-  Null byte `%00` bypasses attempts to add a .php extension to the end of every file name for PHP versions below 5.3.
- Weak allowlist
- Attempts to disallow `https` can be bypassed by supplying input in a different format such as encoded or hexadecimal formats.

# Local File Inclusion

Local File Inclusion occurs when an attacker triggers a webserver to load a local file. The file can be already on the system or a file uploaded by the attacker.

![Damn Vulnerable web app](/images/2022-09-15-22-57-14.png) [Damn Vulnerable Web App](https://github.com/digininja/DVWA) if you're wondering.

Impact: attacker gains access to sensitive files and/or shell access.

Cause: user directly controlling the file loaded by a server. For example, a navigation page which loads the contents of a file.

So breaking that down. 1) user controlled input. 2). file being loaded by a server using that input e.g. using the `includes` or `requires` function in php 3) lack of input sanitisation. 

## How to identify?
- Look for scripts which take filenames as parameters. Check some common system files & directories.

```php
<?php
requires($_GET[ 'page' ]);
?>
```

## How to fix?
- Design stage: prevent user submitted input from being passed to any functions that access the file system.
- Otherwise, sanitise user input. Only allow accessing specific files. access using a long randomised id with the file path stored in a database.

```php
<?php
// Take user input
$file = $_GET[ 'page' ];

// Allowlist. Only allow include.php or file{1..3}.php
if( $file != "include.php" && $file != "file1.php" && $file != "file2.php" && $file != "file3.php" ) {
    // This isn't the page we want!
    echo "ERROR: File not found!";
    exit;
}
?>
```

## Bypass defences
- Null byte `%00` bypasses attempts to add a .php extension to the end of every file name for PHP versions below 5.3.

# References
- https://sushant747.gitbooks.io/total-oscp-guide/content/local_file_inclusion.html
- https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion
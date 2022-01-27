---
title: "AD Password Audit"
subtitle: ""
description: "Ad Password Audit"
date: 2022-01-21T21:48:57+11:00
lastmod: 2022-01-21T21:48:57+11:00
draft: false

author: "JD"
authorLink: ""

tags: [AD,Identity]
categories: [Security]


hiddenFromHomePage: false
hiddenFromSearch: false
featuredImage: ""
featuredImagePreview: ""
toc:
    enable: false
    auto: true

comment:
    enable: true

seo:
    images:
---
## Problem Statement
Accounts with bad passwords, especially accounts with privileged access are the Achilles heel of an organisation’s security. Traditionally IT has tried to impose “strong password policies” such as “Choose a password with an uppercase letter, a number, a symbol and more than 10 characters”. However, a password like “Summer2020!” satisfies ALL those requirements despite being an immesurably weak password.

So, what can we as IT professionals do to reduce bad passwords and mitigate their impact?

- MFA if possible. Yes, it’s been repeated to the ground but only [57% percent of organisations use MFA](https://www.bleepingcomputer.com/news/security/57-percent-of-businesses-use-multi-factor-auth-mfa-says-lastpass/?swcfpc=1#:~:text=Approximately%2057%25%20of%20businesses%20around,on%20data%20from%2047%2C000%20orgs.).
- Disable Legacy authentication.
- Block or limit access to public facing web portals such as OWA, VPN portals and RDweb.
- Impose rate limiting and lockout periods for incorrect authentication attempts.
- Dectect password spraying across multiple accounts by creating honey accounts with login hours set to nothing and set your SIEM to alert when a login attempt is made for that account.
- Audit passwords regularly – I’ll go into more depth later about one way to approach this.
- Encourage users to use passphrases. Yes, I do know that passphrases are weak against dictionary attacks, but much like online games, effective strategies change with the “meta” – the current state of the game. At the moment, as most organisations have password policies which encourage 8 character passwords with special characters and numbers, that’s what attackers are expecting.
- Reduce the ways attacks can obtain NTLMv2 hashes for offline cracking. I.e. disable LLNMR and NBT-NS, block port 445 egress, don’t use domain admin accounts to login to user workstations, enable windows credential guard, minimise use of office macros.
- Password managers – addresses credential reuse and allows for password auditing. I will revisit password managers in another article.
- Implement Password Policies through technical controls and company policies. [Lithnet Password Protection](https://github.com/lithnet/ad-password-protection) and [Azure AD Password Protection](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-password-ban-bad-on-premises) are both great solutions.

## How to Remediate: Password Auditing
One very effective way of assessing the current state of passwords in your organisation is to dump the ntds.dit file and run it against common password lists and masks. Note: depending on your org, this may not be allowed as dumping the ntds.dit file containing all user hashes is risky as if an attacker accesses it, they can guess passwords offline.

1. On a domain controller with an elevated Command Prompt run:

```cmd
ntdsutil "ac in ntds" "ifm" "cr fu c:temp" q q
```

Files will be located in C:\Windows\System32\temp

2. Copy the ntds.dit file to another computer with hashcat installed.

3. Extract the NTLMv2 hashes from the ntds.dat file.

```bash
impacket-secretsdump -system registry/SYSTEM -ntds "Active Directory/ntds.dit" LOCAL -outputfile hashes.txt

#another option
python3 /opt/impacket/examples/secretsdump.py -system registry/SYSTEM -ntds "Active Directory/ntds.dit" LOCAL -outputfile hashes.txt
```

4. Run hashcat against the hashes.txt file. I had the most success with the “dive.rule” combined with rockyou

```bash
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
hashcat -m 1000 -a 0 -o cracked.txt hashes.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/dive.rule –force
```

Windows
```powershell
.\hashcat.exe -m 1000 -a 0 -o .\cracked.txt .\hashes.txt .\rockyou.txt -r .\dive.rule -O
```

5. At the end you can use this awesome tool: [Domain Password Audit Tool (DPAT)](https://github.com/clr2of8/DPAT) created by Carrie Roberts to summarise the results in a neat HTML report.

```bash
python3 ./dpat.py -n hashes.txt -c hashcat.potfile -o report.html
```

Using a Kali VM on my laptop I managed to crack 50% of our organisation’s passwords! You wouldn’t believe how many people had passwords like `<Season><Year>` and `<company name><number>`. It was definitely a worthwhile exercise and was an eye-opener for management.

![AD Passwords](/ad-password-audit-DPAT.png)
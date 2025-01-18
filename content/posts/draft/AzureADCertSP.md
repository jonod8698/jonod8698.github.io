---
title: "AzureADCertSP"
subtitle: ""
description: "AzureADCertSP"
date: 2022-03-17T00:10:36+11:00
lastmod: 2022-03-17T00:10:36+11:00
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

```
# Login to Azure AD PowerShell With Admin Account
Connect-AzureAD 

# Create the self signed cert
$currentDate = Get-Date
$endDate = $currentDate.AddYears(1)
$notAfter = $endDate.AddYears(1)
$pwd = ""
$thumb = (New-SelfSignedCertificate -CertStoreLocation cert:\localmachine\my -DnsName contoso.com -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" -NotAfter $notAfter).Thumbprint
$pwd = ConvertTo-SecureString -String $pwd -Force -AsPlainText
Export-PfxCertificate -cert "cert:\localmachine\my\$thumb" -FilePath c:\contoso.pfx -Password $pwd

# Load the certificate
$cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("c:\contoso.pfx", $pwd)
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())


# Create the Azure Active Directory Application
$application = New-AzureADApplication -DisplayName "PowerShell Universal" -IdentifierUris "https://contoso.com"
New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Jia" -StartDate $currentDate -EndDate $endDate -Type AsymmetricX509Cert -Usage Verify -Value $keyValue

# Create the Service Principal and connect it to the Application
$sp=New-AzureADServicePrincipal -AppId $application.AppId

# Give the Service Principal Reader access to the current tenant (Get-AzureADDirectoryRole)
Add-AzureADDirectoryRoleMember -ObjectId f411a148-6c2b-4173-8222-e703ceae3a23 -RefObjectId $sp.ObjectId # groups administrator

# Get Tenant Detail
$tenant=Get-AzureADTenantDetail
# Now you can login to Azure PowerShell with your Service Principal and Certificate
Connect-AzureAD -TenantId $tenant.ObjectId -ApplicationId  $sp.AppId -CertificateThumbprint $thumb
```

## Title 2

### Subtitle 2
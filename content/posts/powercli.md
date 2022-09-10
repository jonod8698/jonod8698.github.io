---
title: "Automating vSphere with PowerCLI"
subtitle: ""
description: "Powercli"
date: 2020-07-31T18:57:18+10:00
lastmod: 2020-07-31T18:57:18+10:00
draft: false

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

PowerCLI by VMWmare is a tried and tested way of scripting management of a vSphere instance using PowerShell.

## Installing PowerCLI
Open a PowerShell terminal as administrator and run the following commands:
```powershell
#Install PowerCLI
Install-Module VMware.PowerCLI -AllowClobber -Force

#Disable Certificate Verficiation (Don't use this in production)
Set-PowerCLIConfiguration -InvalidCertificateAction Ignore -Confirm:$false

#Disable CEIP Telemetry
Set-PowerCLIConfiguration -Scope User -ParticipateInCEIP:$false

#Save vSphere credentials
New-VICredentialStoreItem -Host <vSphere FQDN or IP> -User "<username>@domain.com" -Password "<password>"

#Test your connection to a vSphere Server
Connect-VIServer <vSphere FQDN or IP>
```

## Instant Clones

An instant clone builds on the advantages of a linked clone by not only sharing common files but also sharing RAM. It’s also parentless too so it’ll still work if the parent VM is deleted.

A CI/CD-like workload such as Intune application testing hugely benefits from instant clones as instead of reinstalling windows, then waiting to go through OOBE hundreds of times, the process can be automated end-to-end. This is critical for catching issues in the install process before pushing Intune to production.

See [William Lam’s guide for further details](https://www.virtuallyghetto.com/2018/05/leveraging-instant-clone-in-vsphere-6-7-for-extremely-fast-nested-esxi-provisioning.html).

The basic process is:

1. Create a Template VM. Install programs/windows updates etc. VMWare Tools must be installed.

2. “Freeze” the VM using a script. This is to minimise the number of delta disks created.

3. Run a script to clone, then “unfreeze” the instant clone.

### Windows Instant Clones

Below is a script to “Freeze” Windows 10 VMs. It does not need to be modified as it receives hostname, IP, DNS, Gateway from the next script. This is a bat file that is run on the source/template VM.

```batch
@echo off

SET LOGFILE=C:customise-ic-log.txt
(call :logit)>>"%LOGFILE%"
exit /b 0
 
:logit

goto:summary
Gets hostname, IP, DNS, Gateway from InstantClone_windows script
Requirements:
- Windows 10
- Vmware tools installed and running on template VM
:summary

set PATH=%PATH%;C:Program FilesVMwareVMware Tools
set IP_ADDRESS=
set HOSTNAME=
set HOSTNAME_ORIG=
for /f "skip=2 tokens=3*" %%A in ('netsh interface show interface') do set INTERFACE_NAME=%%B

echo === Start Pre-Freeze ===
 
timeout 3 > nul

echo === Disabling Network %INTERFACE_NAME% interface ... ===

ipconfig /release %INTERFACE_NAME% > nul 2>&1
netsh interface set interface name="%INTERFACE_NAME%" admin=disable
 
timeout 3 > nul
 
echo === End of Pre-Freeze ===
 
timeout 3 > nul
 
echo === Freezing ... ===
 
vmtoolsd.exe --cmd "instantclone.freeze"
 
timeout 3 > nul
 
echo === Start Post-Freeze when instant clone VM is unfrozen ===

for /F "Tokens=*" %%I in ('vmtoolsd.exe --cmd "info-get guestinfo.ic.ipaddress"') do set IP_ADDRESS=%%I
for /F "Tokens=*" %%J in ('vmtoolsd.exe --cmd "info-get guestinfo.ic.hostname"') do set HOSTNAME=%%J
for /F "Tokens=*" %%G in ('vmtoolsd.exe --cmd "info-get guestinfo.ic.gateway"') do set GATEWAY=%%G
for /F "Tokens=*" %%D in ('vmtoolsd.exe --cmd "info-get guestinfo.ic.dns"') do set DNS=%%D

for /F "Tokens=*" %%K in ('hostname') do set HOSTNAME_ORIG=%%K

timeout 1 > nul
 
echo === Updating IP Address ... === admin below means administratively down or up
netsh interface set interface name="%INTERFACE_NAME%" admin=enable
netsh interface ipv4 set address name="%INTERFACE_NAME%" static %IP_ADDRESS% 255.255.255.0 %GATEWAY%
netsh interface ip set dns "%INTERFACE_NAME%" static %DNS% > nul 2>&1

timeout 2 > nul
echo Original Name: %HOSTNAME_ORIG%
echo New Name: %HOSTNAME%

echo === Updating Hostname ... ===
wmic computersystem where caption='%HOSTNAME_ORIG%' rename '%HOSTNAME%' > nul 2>&1

timeout 4 > nul
 
echo === End of Post-Freeze ===
 
timeout 3 > nul
 
echo === Reboot Guest ===
shutdown -r -t 1
```

On a computer with PowerCLI installed, run the following script. It depends on William Lam’s [InstantClone module](https://github.com/vmware/PowerCLI-Example-Scripts/blob/master/Modules/InstantClone/InstantClone.psm1)

```powershell
<#
.SYNOPSIS Script to deploy instant clones reused ESXi instant clone script.
.NOTES  Reference: http://www.virtuallyghetto.com/2018/05/leveraging-instant-clone-in-vsphere-6-7-for-extremely-fast-nested-esxi-provisioning.html
#>

#This key removes the self-signed cert warning for RDP - don't use it in production.
reg add "HKEY_CURRENT_USERSoftwareMicrosoftTerminal Server Client" /v "AuthenticationLevelOverride" /t "REG_DWORD" /d 0 /f

Import-Module .InstantClone.psm1
Connect-VIServer <vsphere FQDN or IP>
function Show-Menu
{
    param (
        [string]$Title = 'Instant Clone Selection'
    )
    Clear-Host
    Write-Host "================ $Title ================"
    
    Write-Host "1: Press '1' for Fresh Win 10 with Local admin account."
    Write-Host "2: Press '2' for Fresh Win 10 OOBE Autopilot test manual"
    Write-Host "3: Press '3' for Exising POS"
    Write-Host "4: Press '4' for Existing Office User"
    Write-Host "4: Press '5' for Existing Office User"
    Write-Host "5: Press '6' for Win 10 OOBE Autopilot test automatic"
    Write-Host "3: Press '7' for Exising POS - script test"
    Write-Host "4: Press '8' for Existing Office User - script test"
}

Show-Menu -Title 'My menu'
$selection = Read-host "Please make a selection"
switch ($selection)
{
    '1' {
        'selection 1'
        $SourceVM = "Windows 10 Template Fresh"
    } '2' {
        'selection 2'
    } '3' {
        'selection 3'
    } '4' {
        'selection 4'
    }
}

$numOfVMs = 3
$ipNetwork = "192.168.20"
$ipStartingCount=26
$currentIP=$ipStartingCount
$netmask = "255.255.255.0"
$dns = "192.168.20.10"
$gw = "192.168.20.1"
$networktype = "static" # static or dhcp

### DO NOT EDIT BEYOND HERE ###

$StartTime = Get-Date
foreach ($i in 1..$numOfVMs) {
    $newVMName = "Windows-20-$currentIP"

    # Generate random UUID which will be used to update
    # ESXi Host & VSAN Node UUID
    $uuid = [guid]::NewGuid()
    # Changing ESXi Host UUID requires format to be in hex
    $uuidHex = ($uuid.ToByteArray() | %{"0x{0:x}" -f $_}) -join " "

    $guestCustomizationValues = @{
        "guestinfo.ic.hostname" = "$newVMName"
        "guestinfo.ic.ipaddress" = "$ipNetwork.$currentIP"
        "guestinfo.ic.netmask" = "$netmask"
        "guestinfo.ic.gateway" = "$gw"
        "guestinfo.ic.dns" = "$dns"
        "guestinfo.ic.sourcevm" = "$SourceVM"
        "guestinfo.ic.networktype" = "$networktype"
        "guestinfo.ic.uuid" = "$uuid"
        "guestinfo.ic.uuidHex" = "$uuidHex"
    }
    Write-Host "IP Address: $ipNetwork.$currentIP"
    $currentIP++

    # Create Instant Clone
    New-InstantClone -SourceVM $SourceVM -DestinationVM $newVMName -CustomizationFields $guestCustomizationValues

}

$s = 40

#Connect to RDP automatically
do {
    Write-Progress -Activity "Provisioning... $s"
    Start-Sleep 1
    $s--
} while ($s -gt 0)

foreach ($j in 1..$numOfVMs) {

    $fullIP = "192.168.20.$ipStartingCount"
    Write-Output "Connecting to $fullIP"
    $Server=$fullIP
    $User="admin"
    $Password="password1234"
    cmdkey /generic:TERMSRV/$Server /user:$User /pass:$Password
    mstsc /v:$Server
    Start-sleep 4
    $ipStartingCount++
}

$EndTime = Get-Date
$duration = [math]::Round((New-TimeSpan -Start $StartTime -End $EndTime).TotalSeconds,2)

Write-Host -ForegroundColor Cyan  "`nTotal Instant Clones: $numOfVMs"
Write-Host -ForegroundColor Cyan  "StartTime: $StartTime"
Write-Host -ForegroundColor Cyan  "  EndTime: $EndTime"
Write-Host -ForegroundColor Green " Duration: $duration seconds"
```

## Linked Clones
Using a parent VM, linked clones use a snapshot based delta disk to create a clone VM.

Linked clones save disk space as they share the same common base files with their parent VM.

However, linked clones are limited to a chain disk length of 30, still consume a large amount of RAM and are slow to deploy which is why I recommend using [instant clones] which are better in almost every way.

To deploy a linked clone, create a template VM and shut it down.

```

##Creating SQL Linked Clone from a Parent VM "SQLMasterVM"
#variables
Connect-VIServer vc3.domain.com -User administrator@vsphere.local -Password password123

$OSSpec = Get-OSCustomizationSpec -Name 'Windows10_WS1'
$BaseVM = "OOBE Template"
$LinkedVM = "WS1_Test2"

# Delete snapshots on the Parent VM
Get-Snapshot -VM $BaseVM | Remove-Snapshot -Confirm:$false
Start-Sleep -Seconds 2

#Create snapshot
New-Snapshot -VM $BaseVM -Name "Linked-Snapshot" -Description "Snapshot for linked clones for $LinkedVM"
 
#Gather information of the created snapshot
$snapshotParent = Get-Snapshot -VM $BaseVM | Select Name
$snapshotParent = $snapshotParent.Name
Start-Sleep -Seconds 5
 
#Create Linked Clone referencing snapshot and start the VM.
New-VM -Name $LinkedVM -VM $BaseVM -Datastore "Datastore1" -VMHost esxi3.domain.com -OSCustomizationSpec $OSSpec -LinkedClone -ReferenceSnapshot $snapshotParent -DiskStorageFormat Thin
Start-VM -VM $LinkedVM
```
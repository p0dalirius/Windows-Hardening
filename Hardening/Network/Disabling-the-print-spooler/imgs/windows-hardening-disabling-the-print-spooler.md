---
title: "Windows Hardening - Disabling the Print Spooler"
date: 2021-07-05T12:00:00Z
images: ["/en/articles/windows-hardening-disabling-the-print-spooler/thumbnail_en.png"]
description: "Windows Print Spooler is a service with many vulnerabilities. In this we will see 3 methods to disable this service."
categories: []
tags: []
keywords: ["vulnerability", "fix", "ms", "rprn", "microsoft", "print", "spooler", "printer", "printerbug", "printnightmare", "service", "disable", "domain", "controler"]
type: "posts"
---

## Introduction

The Microsoft Windows operating system Print Spooler is a service for managing print jobs. The executable for this service is `spoolsv.exe` and this service is **enabled by default** on most Microsoft Windows systems.

It has been impacted by a large number of vulnerabilities over the years, and was used as the spread vector of the virus [Stuxnet](http://virus.wikidot.com/stuxnet).

Recently, two new critical vulnerabilities were released. The [CVE-2021-1675](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-1675) (also called PrintNightmare) and the [CVE-2021-34527](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34527).

In order to fix these vulnerabilities, it is generally recommended to disable the print spooler service on Windows servers that do not require it. In the following sections, we will see 3 methods to disable this service.

## Detection of print spooler by attackers

During the recognition phase of a pentest or simply when attacking the internal network of a company, the attackers carry out scans of the machines. These scans make it possible to detect the services and their versions that are running on the machines of the network. The print spooler service being an RPC service, an attacker can check if it is active with the following `rpcdump.py` command:

```
# rpcdump.py 192.168.1.21 | grep MS-RPRN -A6
```

If the service is active, it will be displayed in the list of RPC services and will be found by the `grep MS-RPRN -A6`:

![](/articles/windows-hardening-disabling-the-print-spooler/ms-rprn-activated.png)

## Disabling the Print Spooler service via the graphical interface

In order to deactivate the `Print Spooler` service via the graphical interface, you must use the Windows Services Manager. Then select the `Print Spooler` service from the list of services of this interface and click on properties. Stop the service, then deactivate it from the `StartupType` drop-down menu.

![](/articles/windows-hardening-disabling-the-print-spooler/gui.gif)

## Disabling the Print Spooler service through a GPO

It is also possible to disable the service on multiple machines in the domain using a Group Policy Object (GPO). To do this, you need to go into the Group Policy Management Console

<!-- phrase -->

![](/articles/windows-hardening-disabling-the-print-spooler/gpo.gif)

## Disabling the Print Spooler service via powershell

To disable the Print Spooler service via powershell, we will first check the status of the service, using the powershell [Get-Service](https://docs.microsoft.com/fr-fr/powershell/module/Microsoft.PowerShell.Management/get-service?view=powershell-7.1) command:

```powershell
Get-Service -DisplayName "Print Spooler"
```

![](/articles/windows-hardening-disabling-the-print-spooler/ps1_1.png)

We see in the screenshot above that the `Status` of the service is `Running`, which means it is enabled and running. Therefore we will need to stop it before disabling it. To stop it we will use the following [Get-Service](https://docs.microsoft.com/fr-fr/powershell/module/Microsoft.PowerShell.Management/get-service?view=powershell-7.1) powershell command, allowing to select the service, and [Set-Service](https://docs.microsoft.com/fr-fr/powershell/module/Microsoft.PowerShell.Management/set-service?view=powershell-7.1) to stop it:

```powershell
Get-Service -DisplayName "Print Spooler" | Stop-Service
```

![](/articles/windows-hardening-disabling-the-print-spooler/ps1_2.png)

You must then disable the service by setting the `StartupType` to the value` disabled` like this:

```powershell
Get-Service -DisplayName "Print Spooler" | Set-Service -Status stopped -StartupType disabled
```

![](/articles/windows-hardening-disabling-the-print-spooler/ps1_3.png)

We can also check in the service management console that the print spooler is disabled:

![](/articles/windows-hardening-disabling-the-print-spooler/ps1_4.png)

### In summary

To disable the Windows Print Spooler service, type the following powershell commands:

```powershell
Get-Service -DisplayName "Print Spooler"
Get-Service -DisplayName "Print Spooler" | Stop-Service
Get-Service -DisplayName "Print Spooler" | Set-Service -Status stopped -StartupType disabled
```

## References
 - https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rprn/d42db7d5-f141-4466-8f47-0a4be14e2fc1
 - https://docs.microsoft.com/fr-fr/defender-for-identity/cas-isp-print-spooler
 - https://docs.microsoft.com/fr-fr/powershell/scripting/samples/managing-services?view=powershell-7.1

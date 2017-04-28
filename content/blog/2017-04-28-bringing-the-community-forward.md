---
author: "Christopher Hunt"
authortwitter: "https://twitter.com/logicaldiagram"
category: ["Blog"]
comments: true
date: "2017-04-28T12:12:09-05:00"
sharing: true
tags: ["powershell", "gallery", "community"]
title: "Bringing The Community Forward"
slug: "bringing-the-community-forward"
draft: false
---

# An unhealthy obsession with numbers

I like to watch numbers move which might have some relationship to being a successful operator of complex systems.
Unfortunately it extends outside of looking for pattern in streams of monitoring data and I can get a bit obsessed watching Google Analytics for my blog or the download count for [PoshSpec](https://www.powershellgallery.com/packages/poshspec/).

I have been keeping an eye on PoshSpec numbers in the [Statistics](https://www.powershellgallery.com/stats/packages) page of the Gallery just hoping to crack the top 50. However, if you scroll up, you notice a pattern.
Azure. Azure. Azure...
In fact, when you filter out the modules to remove Azure and the "experimental" DSC resources, there are only about a dozen community modules in the top 100.

Out of curiousity, I wanted to find out what the top 100 would look like if you excluded the 1st party modules. It's pretty easy to get some useful data out of the PSGallery API.

```
$modules = Find-Module -Repository PSGallery

$normalized = $modules |
    Select-Object Name, @{Name = "Downloads"; Expression = {$_.AdditionalMetadata.downloadCount -as [int]}}, Author

$script:i = 1
$normalized |
    Where {$_.Author -ne 'Microsoft Corporation' -and $_.Author -ne 'PowerShell DSC'} |
        Sort Downloads -Descending |
        Select @{n = '#'; e = {$script:i++}}, Name, Downloads, Author -first 100
```

# Community Top 100

Here is the top 100 (mostly) community published modules based on total download since publication.
I previously have not heard of a lot of these modules and immediately went to investigate what the do.
Maybe you'll discover a new module that you wished you had and never knew existed.


```text
  # Name                                Downloads Author
  - ----                                --------- ------
  1 Carbon                                 299504 Aaron Jensen
  2 PSWindowsUpdate                        206988 Michal Gajda
  3 Posh-SSH                               149275 Carlos Perez
  4 DockerMsftProvider                      76905 jayshah
  5 Pester                                  57298 Pester Team
  6 SMLets                                  53228 Sundqvist Truher Wright Gritsenko
  7 ISESteroids                             28572 Dr. Tobias Weltner
  8 posh-git                                28548 Keith Dahlby and contributors
  9 poshspec                                24427 Chris Hunt
 10 GoogleMap                               19858 Prateek Singh
 11 NuGet                                   19557 Jason
 12 ProjectOxford                           19343 Prateek Singh
 13 cChoco                                  16099 Chocolatey Software Lawrence Gripper ...
 14 ACMESharp                               15232 https://github.com/ebekker
 15 PlannerOne.Deployment                   14970 François Le Rolland (f.lerolland@orte...
 16 ImportExcel                             14610 Douglas Finke
 17 Pscx                                    13950 PowerShell Community Developers
 18 posh-docker                             13273 Sam Neirinck
 19 PowerShellCookbook                      12155 Lee Holmes
 20 TaskRunner                              10751 Ajay Arora
 21 xSystemSecurity                         10706 Arun Chandrasekhar
 22 OMSDataInjection                        10161 Tao Yang
 23 AWSPowerShell                            9809 Amazon.com Inc
 24 AzureAutomationAuthoringToolkit          9683 AzureAutomationTeam
 25 ARTools                                  9444 Adrian Rodriguez
 26 PolicyFileEditor                         8966 Dave Wyatt
 27 psake                                    8731 James Kovacs
 28 SharePointPnPPowerShellOnline            8412 SharePoint Patterns and Practices
 29 VstsTaskSdk                              8041 Microsoft
 30 CredentialManager                        7275 Dave Garnar
 31 NTFSSecurity                             7078 Raimund Andree
 32 Octoposh                                 6901 Dalmiro Granias ; http://about.me/dal...
 33 xFirefox                                 6875 Microsoft
 34 dbatools                                 6754 Chrissy LeMaire
 35 xPowerShellExecutionPolicy               6655 OneScript Team
 36 PSJira                                   6326 Joshua T
 37 MVA_DSC_2015_DAY1                        6124 Jeffrey Snover & Jason Helmick
 38 MVA_DSC_2015_DAY2                        6006 Jeffrey Snover & Jason Helmick
 39 xExchange                                6006 Mike Hendrickson Jason Walker Michael...
 40 xReleaseManagement                       5977 Donovan Brown
 41 xChrome                                  5831 Microsoft
 42 PSSlack                                  5471 Warren F.
 43 WinSCP                                   5389 Thomas J. Malkewitz @dotps1
 44 DellBIOSProvider                         4825 Dell BizClient Team
 45 cNtfsAccessControl                       4749 Serge Nikalaichyk
 46 xDatabase                                4704 Microsoft
 47 HPWarranty                               4673 Thomas J. Malkewitz @dotps1 and Ben @...
 48 platyPS                                  4590 PowerShell team
 49 PSDeploy                                 4573 Warren Frame et al
 50 AWSPowerShell.NetCore                    4568 Amazon.com Inc
 51 ISEModuleBrowserAddon                    4488 MicrosoftCorporation
 52 PoshRSJob                                4366 Boe Prox
 53 SCOrchDev-Exception                      4337 Ryan Andorfer
 54 7Zip4Powershell                          4182 Thomas Freudenberg
 55 PsHosts                                  4169 Richard Szalay
 56 AU                                       4134 Miodrag Milic
 57 BuildHelpers                             4120 Warren Frame
 58 ChocolateyGet                            3916 Jianyun
 59 xInternetExplorerHomePage                3864 OneScript Team
 60 DSInternals                              3755 Michael Grafnetter
 61 VMware.VimAutomation.Core                3593 "VMware"
 62 localaccount                             3509 Sean P. Kearney
 63 PSExcel                                  3450 Warren Frame
 64 GraniResource                            3388 guitarrapc
 65 PasswordState                            3348 Brandon Olin
 66 xOU                                      3280 Visual Studio ALM Rangers
 67 xBitlocker                               3268 Mike Hendrickson
 68 VMware.VimAutomation.Sdk                 3244 "VMware"
 69 newtonsoft.json                          3222 jakub.pawlowski
 70 VMware.VimAutomation.Cis.Core            3165 "VMware"
 71 VMware.VimAutomation.Common              3164 "VMware"
 72 VMware.VimAutomation.Vds                 3160 "VMware"
 73 SnippetPx                                3098 Kirk Munro
 74 VMware.VimAutomation.Srm                 3086 "VMware"
 75 VMware.VimAutomation.vROps               3078 "VMware"
 76 VMware.VimAutomation.License             3073 "VMware"
 77 VMware.VimAutomation.HA                  3067 "VMware"
 78 VMware.VimAutomation.HorizonView         3029 "VMware"
 79 VMware.VimAutomation.Cloud               3021 "VMware"
 80 VMware.ImageBuilder                      2976 "VMware"
 81 VMware.VimAutomation.PCloud              2957 "VMware"
 82 xWindowsRestore                          2895 OneScript Team
 83 VMware.DeployAutomation                  2821 "VMware"
 84 VMware.VimAutomation.Storage             2772 "VMware"
 85 cWindowsOS                               2749 Ravikanth Chaganti
 86 SSH                                      2701 JoeLevy
 87 xWindowsEventForwarding                  2666 PowerShell Team
 88 PSSQLite                                 2635 ramblingcookiemonster
 89 VMware.VimAutomation.StorageUtility      2624 "VMware"
 90 VMware.PowerCLI                          2618 VMware
 91 BurntToast                               2617 Joshua (Windos) King
 92 VMware.VumAutomation                     2611 "VMware"
 93 Saritasa.General                         2600 Anton Zimin
 94 GistProvider                             2598 Doug Finke
 95 PSLogging                                2582 LucaSturlese
 96 TaskModuleSqlUtility                     2541 Microsoft
 97 cHyper-V                                 2510 Ravikanth Chaganti
 98 Microsoft.Xrm.Data.Powershell            2504 {Kenichiro Nakamura, Sean McNellis}
 99 cIBMInstallationManager                  2495 Denny Pichardo
100 MSI                                      2494 Heath Stewart
```

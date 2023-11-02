---
layout: post
title: "Citrix WEM Updated Start-Up Scripts"
permalink: "/citrix-wem-updated-start-up-scripts/"
description: "Updated startup scripts to deal with Citrix WEM Cache"
categories: [Apps and Desktops, Citrix, Cloud, WEM, Windows, XenApp]
redirect_from: 
    - /2019/09/11/citrix-wem-updated-start-up-scripts
    - /2019/09/11/citrix-wem-updated-start-up-scripts/
image:
  path: /assets/img/citrix-wem-updated-start-up-scripts/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

I have been recommending in all deployments ever since I started with WEM, to implement the very simple "WEM Agent Refresh" Start-Up Script. Every deployment I have seen, touched or been involved that uses this basic concept of "refresh my cache at start-up" has no issues relating to cache, delayed start-up or inconsistent behaviours. It just works.

With the release of Citrix WEM 1903 (Cloud) and 1909 (on-prem), there are some major changes to the path structures for WEM as the team begins the rebranding process away for Norskale. This of course results in some changes to how the start-up scripts are going to run and process.

The following changes are going to occur so be ready:

1.  A new clean installation of the WEM Agent will result in a complete change of Service Names and Folder Structures as below
    -  The new Service name is: `Citrix WEM Agent Host Service`
    -  The new process name is: `Wem.Agent.Service.exe`
    -  The new path structure is: `%ProgramFiles%\Citrix\Workspace Environment Management Agent`
2.  An upgraded installation of the WEM agent will result in partial changes to your environment:
    -  The new Service name is: `Citrix WEM Agent Host Service`
    -  The new process name is: `Wem.Agent.Service.exe`
    -  The path on the file system will not be altered and will remain as it was: `%ProgramFiles%\Norskale\Norskale Agent Host`

Be aware also that in both clean and upgraded deployments, the Windows Event logs will change from `Norskale Agent Service` to `WEM Agent Service`

| | **Old (Pre Cloud Service 1903 and On-Prem 1909)** | **New (Post Cloud Service 1903 and On-Prem 1909)** |
| :-- | :-- | :-- |
| Installation path | `%ProgramFiles%\Norskale\Norskale Agent Host` | `%ProgramFiles%\Citrix\Workspace Environment Management Agent` |
| Service name | `Norskale Agent Host Service` | `Citrix WEM Agent Host Service` (`WemAgentSvc`) |
| Process Name | `Norskale Agent Host Service.exe` | `Citrix.Wem.Agent.Service.exe` |
| Event Logs | `Norskale Agent Service` | `WEM Agent Service` |

As such, I have written a couple of replacement scripts with a few more brains and checks than the original batch fun that is publicly available. The scripts (both PowerShell and batch) do the following

1.  Check to see what version of WEM is installed based on the Service Name (I did not say I was smart).
2.  If the original Norskale Service is returned, then the script will simply restart the `Norskale Agent Service_`(and the `Netlogon` accordingly), and then refresh the cache the same as the original script does
3.  If the new Service is returned, then the Script will check to see if this is an upgrade or a clean install. If it is a clean install, it will stop the appropriate services, refresh the cache via the new pathing and pat itself on the back. If it is an upgraded install, it will leverage the old path to refresh the cache instead

I have seen multiple discussions posts and environments that have needed to all periods of sleep and timeouts in between certain components in their environments, these scripts should be easy enough to alter and add to accordingly.

## Script

All scripts are available in my [Github Repository](https://github.com/JamesKindon/Citrix/tree/master/Citrix%20WEM%20Startup%20Scripts)

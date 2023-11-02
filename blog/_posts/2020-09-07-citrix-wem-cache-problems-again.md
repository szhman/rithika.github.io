---
layout: post
title: "Citrix WEM Cache Problems…. Again."
permalink: "/citrix-wem-cache-problems-again/"
description: Scripting the crap out of the WEM Cache....Again
categories: [Apps and Desktops, Citrix, Cloud, PowerShell, WEM]
redirect_from: 
    - /2020/09/07/citrix-wem-cache-problems-again
    - /2020/09/07/citrix-wem-cache-problems-again/
image:
  path: /assets/img/citrix-wem-cache-problems-again/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

I have been beating my head against the wall with WEM Cache issues for what feels like a lot of years now, customer after customer, deployment after deployment hurt in one way, shape or form until the WEM cache is taken care of on boot. If the cache is appropriately slapped, then WEM does what it is supposed to.

There are several different failure scenarios I have seen and I have written basic batch and PowerShell scripts that I have used across all environments to tackle and bring back stability. There are also several iterations of the same sort of logic floating around, I know [Kasper](https://twitter.com/KasperMJohansen) has one, and a few of the lads on the World of EUC slack channel have had to customise instances of these scripts to suit their environments, primarily with timeouts and delays etc.

The latest and arguably the most frustrating scenario I have been seeing is the cache in WEM Service deployments will for some reason "break". By "break", I mean that it will look healthy, it will appear to Sync, but it will never bring down changes made in the WEM console, the refresh task will simply show no changes. The fix consistently has been to delete the cache on startup and bring down a new one, but this results in a nasty scenario where if you can’t pull down the cache, you are in some strife…not pretty.

As such, I have written a new script in an attempt to tackle all scenarios with one script, it’s been beefed up and simplified all at the same time, details as follows:

-  I no longer support upgraded installations of WEM from the pre-1903 release. If you had WEM pre-1903 and upgraded, you know the paths changed. I got sick of trying to cater for this, and given its now an old release, I am ditching support for it. To use the upgraded scripts, you will need to uninstall WEM and reinstall WEM so that the pathing is up to date and you are on a clean installation. If you want the old scripts, they are retained in Github
-  The script now logs everything
-  The script offers a ***DeleteWemCache*** parameter, specifically targeted at WEM service deployments. If you choose this (It's set to false by default), I have a built-in insurance policy that does the following:
    -  Stops the WEM services
    -  Backs up the existing WEM cache files
    -  Starts the WEM services and forces a refresh
    -  If the refresh fails and the cache files do no get created, it tries again (stop, start, refresh)
    -  If the refresh fails for a second time (it gives it 30 seconds) then we assume connectivity fails and simply stop the services, restore the cache files, start the services and forces a sync (which by rights should fail anyway). Better to have old cache than no cache
-  The script is now 100% PowerShell only, I have no idea how to do any of the above in batch, and quite frankly – it’s batch…

There is an enhancement coming which should sort out WEM cache problems once and for all, but for now, if you want stability and consistency, then it’s business as usual – deploy the script and move on in life. I have yet to see a WEM deployment function consistently without a form of this logic in place.

## Summary and Script

Hope it helps. Fingers crossed this is the last time I have to touch this. Deploy it as a startup script by GPO and you should be fine.

[Script](https://github.com/JamesKindon/Citrix/blob/master/Citrix%20WEM%20Startup%20Scripts/RestartWEMServices.ps1)
---
layout: post
title: "The why does it of Citrix WEM Cache"
permalink: "/the-why-does-it-of-citrix-wem-cache/"
description: "Understanding why WEM cache does what WEM cache does..."
categories: [Apps and Desktops, Citrix, WEM]
redirect_from: 
    - 2020/10/15/the-why-does-it-of-citrix-wem-cache
    - 2020/10/15/the-why-does-it-of-citrix-wem-cache/
#Image: "/assets/img/the-why-does-it-of-citrix-wem-cache/cache.png"
image:
  path: /assets/img/the-why-does-it-of-citrix-wem-cache/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Citrix Workspace Environment Management (WEM) has continually had challenges in non-persistent environments when dealing with WEM Cache management. Administrators and consultants have been struggling with consistency in WEM processing when the Cache is in play.

If we take a trip down memory lane, it is important to revisit a couple of fundamentals about the WEM agent. The Agent is broke into two key components

-  The **WEM agent host service (Host Service)**. This service is responsible for machine settings and runs as a windows service. This component ignores anything to do with WEM cache configurations that are made in the WEM console and will always apply from cache should there be an outage
-  The **WEM agent (Session Service).** This is the component that is responsible for processing user assignments and applying user settings. This typically presents the splash screen on logon, and is directly impacted by Cache and offline processing settings in the WEM console

Two key behaviours of the WEM agent in relation to the connectivy and cache:

-  The WEM agent will check broker connectivity periodically and action is processing state accordingly
-  The WEM agent always syncs agent cache periodically. This is a configurable setting for the scheduled sync, however is randomized on startup to avoid boot storms

Offline mode is designed to provide a fallback to a subset of the WEM database (Cache) on the local endpoint, providing redundancy and continuity should connectivity to the WEM broker be lost. Regardless of the configuration within the WEM Console (Enable offline mode), the WEM agent service will pull down a cache to the local endpoint. How that cache is processed depends on a few conditions.

WEM offers several settings when it comes to utilising the cache. There was a [very nice article](https://www.citrix.com/blogs/2020/03/23/workspace-environment-management-agent-caching-explained/) written a little while back here which explains the behaviours. A brief summary is below:

-  _Enable Offline Mode_. This setting controls the user processing component of WEM settings should connectivity be lost to the broker. If connectivity is lost, _offline_ or _Cache_ processing will occur. This only effects the agent processing, and not the host service. The agent host service will always process from cache in an offline scenario regardless of this setting
-  _Use Cache Even When Online_. This functionality is designed to address situations where connectivity to the WEM brokers is not consistent, and forces processing from the offline cache regardless of connectivity to the Broker
-  _Accelerate Actions Processing_. This option combines the best of an online and offline world. Its job is to allow the WEM agent to process user assigned actions (applications, drives, environment variables etc) from Cache, whilst checking and applying environment settings from the broker itself. This results in a massive reduction of calls to the WEM broker and reduces the processing time for user actions. It does, however, rely on the cache being healthy and regularly updated

If neither “Use Cache Even When Online” or “Accelerate Actions Processing” are configured, WEM will act in a very simple manner: **read from the broker if available, otherwise use cache**.

## Importance of the Cache

![Cache](/assets/img/the-why-does-it-of-citrix-wem-cache/cache.png)

So why is the Cache so important if it is only used for fallback should the Broker not be accessible? Well, there is a little more to this story:

-  When the WEM services start, they are started as the network stack is brought online, effectively meaning that every process start will check and utilize the cache. Effectively, _<u>every</u>_ startup is an offline startup from a WEM perspective if the Cache is present
-  On machine boot, the WEM agent service checks to see if there is a copy of the WEM cache available on the local machine. If there is, then the initial sync command is bypassed entirely and not run, resulting in a stale cache. The longer your machines are in production since the last image update (and assumed cache refresh) the bigger the gap in your current config and the cache
-  On the next scheduled sync run, changes are brought into cache, but this may be after users have already logged on and been presented with stale configurations. Come to the next scheduled user refresh (this is not a cache refresh), the environment comes back into line
-  At the first scheduled refresh interval, the WEM agent service will try and talk to the Broker (completely randomised to avoid a boot storm) at which point it will flick over to online mode and ignore the cache until required. This can lead to some inconsistent behaviour if the WEM cache is out of sync
-  WEM is completely dependent on a stable network connection when talking to the brokers, any form of delay or network issue will force WEM back into leveraging the cache, if the cache is not synced, then the environment may once again start showing inconsistent results for the end-user

Many of us in consulting or administrative roles have been deploying scripts that execute at the startup of machines, to restart WEM services and force a cache refresh. This simple mitigation is common across hundreds, if not thousands of WEM deployments, and has been accepted as a necessary evil to mitigate the varying challenges associated with the WEM cache. The problems we are aiming to address with these scripts primarily appear to effect non-persistent environments with the cache in the default location, and primarily MCS rather than PVS (but not always). In fact, the first instances of the scripts we now use commonly were provided for PVS environments, with no mention of MCS at all.

Both WEM on-premises and WEM service offerings suffer from these challenges. In WEM service deployments, the inconsistency and cache problems can also present additional problems. There are many instances in non-persistent environments where the WEM cache will appear to successfully sync with the WEM Service, however, no actual changes are reflected in the cache itself. The only mitigation is to physically delete the WEM cache on machine boot resulting in a rebuild. The reason for this behaviour is as follows:

-  Each agents WEM cache has a _unique_ GUID associated with it
-  When an agent reports into the WEM service, the service checks the GUID and for the first machine that reports in owning this GUID, WEM pushes any required changes to its Cache
-  When additional agents report in, WEM identifies the same GUID (Cache) and says "_hey, I have already pushed changes to you, see you later_". No errors, no failures, because the WEM service has done what it believes is correct
-  Should the administrator make another change in the WEM console, whichever agent happens to check in first will pull that change, but none of the other agents will due to the logic above

This behaviour explains why deleting the Cache on machine boot brings results in consistency. Post deletion, a new Cache is built with a new GUID making the WEM agent appear unique again

PVS environments are not as heavily impacted typically, and this is because many administrators redirect the WEM cache to the persistent disk. This practice was one that was of questionable benefit for a long time but turns out to have had a hidden win as the cache is both unique, and tends to be up to date given its persistent nature

Citrix has a code release on the way to address these challenges and provide more fine-grained capability and control to handle the above scenarios. Until then, there is some updated guidance for WEM in non-persistent environments:

-  Stop the WEM Service and remove the WEM cache from your golden image before sealing and releasing (this will be the very last thing that should occur given that there is a dependency for the netlogon service). Both for PVS and MCS with the cache in the default location. This ensures that at every machine boot, WEM will automatically try and create a brand-new unique cache
-  Alternatively, [deploy a startup script like mine](https://jkindon.com/2020/09/07/citrix-wem-cache-problems-again/) to delete and refresh the WEM cache on startup. The added benefit of this approach is that there is a failback capability where if WEM cannot talk to the Brokers and create a new cache, there is at least a stale cache to process. _Some may well be better than none_, particularly in an environment that is not changing regularly. This is an interim solution until the new code drop which will allow for a cache to be purged appropriately in non-persistent environments

## Summary and Thanks

We wanted to provide this information even though a code drop is on the way which will negate the need for any manual input because this has been a challenge for many years across many, many deployments. Most tickets and issues associated with WEM have been due to cache inconsistencies and challenges. Hopefully understanding the technical reasons as to why this has been occurring, will put some minds at ease, and refresh confidence levels in utilising WEM for environment management across Citrix deployments

This article is the culmination of many, many hours of work by a number of contributors. We (this is as much their article as it is mine) want to thank the following for input into helping diagnose, explain, and bring forward the code fix to address this ongoing challenge:

-  [Wayne Liu](https://twitter.com/waynecitrix)
-  [Anthony Shi](https://twitter.com/anthony_ctx)
-  Lu sun
-  [Cheng Zhang](https://twitter.com/ChengZhang_NKG)
-  [Pierre Marmignon](https://twitter.com/pmarmignon)
-  Chao Wang

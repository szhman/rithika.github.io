---
layout: post
title: "Improve the WEM Cloud Service Agent Processing Speed"
permalink: "/improve-the-wem-cloud-service-agent-processing-speed/"
description: Making the WEM Cloud Service Process nice and fast
categories: [Apps and Desktops, Citrix, Cloud, WEM, Windows, XenApp]
redirect_from: 
    - /2020/02/03/improve-the-wem-cloud-service-agent-processing-speed
    - /2020/02/03/improve-the-wem-cloud-service-agent-processing-speed/
#Image: "/assets/img/improve-the-wem-cloud-service-agent-processing-speed/Speed.jpg"
image:
  path: /assets/img/improve-the-wem-cloud-service-agent-processing-speed/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

The Citrix Cloud WEM Service looks and feels, for the most part, the same as the On-Premises iteration of the solution, however, there are some challenges which have been known to introduce, let's say "a less than desirable experience" when moving to the service release.

In this post, I will focus on the challenge introduced with the WEM Agent talking to the Cloud Service, and the processing speed impacts that may be introduced in some environments, and how to mitigate and architect your deployment accordingly. To start with, let's take a quick look at the WEM Service Architecture below:

[![WEM Cloud Service Architecture]({{site.baseurl}}/assets/img/improve-the-wem-cloud-service-agent-processing-speed/Architecture.png)]({{site.baseurl}}/assets/img/improve-the-wem-cloud-service-agent-processing-speed/Architecture.png)

The Citrix Cloud Connector is utilised by the WEM Agent to handle registrations, authentication and infrastructure-related components. The WEM Agent, however, communicates directly outbound to the WEM Cloud Service over port 443.

In the Cloud architecture, noticeable agent processing delays will occur depending on your network connectivity and location, sometimes an intolerable level of processing time is introduced which can negatively impact the user experience. This is due to the red line in the diagram above, which reflects processing directly via the Infrastructure Services in the Cloud, rather than via the Citrix Cloud Connectors (Blue Line).

The WEM Service currently operates out of the US and EU control planes only (poor APJ), which immediately starts to explain some challenges when you look at the agent wanting to talk directly to the Cloud, and process once it receives its information.

## Mitigating the challenge

To mitigate this challenge, you can force WEM to process based on the local cache even when operating online. This forces the WEM agent to look at the last known cache held on the endpoint, and process based on that configuration, drastically decreasing processing time.

[![Use Cache Even When Online]({{site.baseurl}}/assets/img/improve-the-wem-cloud-service-agent-processing-speed/CacheWhenOnline.png)]({{site.baseurl}}/assets/img/improve-the-wem-cloud-service-agent-processing-speed/CacheWhenOnline.png)

This isn't a new feature of WEM, it has has been around since the start, but it’s never properly worked until now.

As of version 2001.1.0.1 of the WEM Service agent (and conversely the 1912 version of the on-premises agent), WEM now dignifies this configuration and will process from the cache as it should.

The internal bug ID fix for this is **"The Use Cache Even When Online option on the Administration Console > Advanced Settings > Configuration > Agent Options tab might not work. [WEM-6118]"**

Processing from the cache introduces some new considerations though which should be understood, implemented and monitored, namely:

-  The cache must be up to date, else you will process old configurations. Console changes are not immediately reflected on the end-user environment and will not appear until a cache refresh has occurred
-  You can reduce the cache sync interval in the WEM console (Agent Cache Refresh Delay), else you can consider a scheduled task to force a regular refresh of the cache if you are making regular changes

**UPDATE! 11.02.19. Important to note that the following occurs regardless of your cache refresh settings**
{:.special_note}

"The minimum interval for cloud agent cache sync is 15 mins, there is a randomized mechanism to control it. If you config the interval to 5 minutes, the true interval will be a random one between 7.5 mins to 30 minutes"
{:.warning}

[![Agent Cache Refresh Setting]({{site.baseurl}}/assets/img/improve-the-wem-cloud-service-agent-processing-speed/ServiceOptions.png)]({{site.baseurl}}/assets/img/improve-the-wem-cloud-service-agent-processing-speed/ServiceOptions.png)

-  You may need to alter start-up scripts with some better logic. I worked recently on a deployment on Windows Server 2019 where we needed to stop the WEM Services on boot (normal), delete the local cache database files (not normal), start the WEM services and force a refresh (normal) wait for 10 seconds and restart the WEM services again (not normal) else the agent simply wouldn't launch for new users. This is not the norm and I have never seen it before, but hey - it is what it is. It’s not hard to extend my [basic scripts](https://github.com/JamesKindon/Citrix/tree/master/Citrix%20WEM%20Startup%20Scripts)
-  You will lose the last connection and recently connected status in the console. As long as the Cache is in a healthy sync state, this is cosmetic only

[![Agents]({{site.baseurl}}/assets/img/improve-the-wem-cloud-service-agent-processing-speed/Agents.png)]({{site.baseurl}}/assets/img/improve-the-wem-cloud-service-agent-processing-speed/Agents.png)

## What about On-Prem?

The same concepts can apply to environments with connectivity issues to WEM brokers, be it known or unknown challenges, I have worked in environments where agent processing is simply slow - no errors, no feedback, nothing obvious.

This is usually down to a form of network connectivity challenges. If you are running the latest version of WEM (1912) then you can now process in online mode as expected, however, if you are not, and are suffering from slow processing, there is a little trick you can implement to force WEM into processing from cache with a Citrix ADC.

With an ADC in place, you typically load balance ports 8285 and 8286 (and now of course 8288 but that's irrelevant here) for WEM agent connectivity to the Broker. In a broken state, when you enable processing from cache and WEM detects that the Brokers are actually online, the setting is ignored and standard processing occurs. If you were to lose the brokers however, the agent will process in offline mode, and process from the cache. So, with an ADC, you can simply disable the VIP for Port 8286 and make the agent think that the brokers are offline. Cache Synchronization occurs across port 8285 so leave that alone, and you can continue to sync in a supported fashion, whilst processing from the cache.

**Note** that if you add <u>new</u> WEM agents into the mix, you will need to enable 8286 to allow them to register for the first time. So whilst it's not pretty, it gets passed the bug. That, or upgrade and move on in your life.
{:.special_note}

This is not a new fix, my buddy Munro figured this out a couple of years back, but it took a while to convince people that the bug was real – credit where it’s due, it is now fixed and there are also future improvements on the way to assist with the processing speed.

## Summary

No Cloud Service is without challenges and considerations. There are always unforeseen or unexpected impacts when both designing, and consuming Cloud Services – WEM is no different. Credit to the product team, they are responsive and active in developing fixes and improved functionality for the service. Hopefully, for those currently suffering from unexpected delays or processing challenges, this post can help with a win or two in making your environment functional and fast.

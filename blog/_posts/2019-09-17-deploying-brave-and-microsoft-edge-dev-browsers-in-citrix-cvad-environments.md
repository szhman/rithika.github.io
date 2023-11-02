---
layout: post
title: "Deploying Brave and Microsoft Edge Dev Browsers in Citrix CVAD environments"
permalink: "/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/"
description: "Deploy the Brave and Edge Browsers in CVAD"
categories: [Apps and Desktops, Browsers, Citrix, Windows]
redirect_from: 
    - /2019/09/17/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments
    - /2019/09/17/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/
image:
  path: /assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

I like choice, and I really like privacy, so naturally, I am drawn to the [Brave browser](https://brave.com/) due to its underpinning fundamentals of “privacy first”. In short, it’s the Chrome engine with a load of inbuilt privacy and tracking protection similar to uBlock Origin, an extension for Google Chrome but all built-in.

I had some fun getting it to work in a Citrix environment but after some playing around have a neat deployment.

Important to note, I am not providing advice to move to Brave, it’s not really enterprise-ready (if you want all the controls that something like Chrome or IE give you in a supported fashion), however should you choose to have a play, the below should get you started.

Conversely, the same symptoms and challenges are raised with the new [Chromium-based Microsoft Edge browser](https://www.microsoftedgeinsider.com/en-us/download?form=MI13E8&OCID=MI13E8), so I documented both.

## The Problem

When you install either Brave or Microsoft Edge Dev on a server or desktop with the Citrix VDA installed, launching either process will result in a white window which will spank your CPU and leave your session effectively useless

[![Fail]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/Fail.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/Fail.png)

Task Manager shows us a load of processes launched, but nothing plays

[![Task Manager]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/Task Manager.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/Task Manager.png)

## The Solution

No surprises, but the underlying cause of this is the Citrix API hooks latching themselves onto either the `Brave.exe` or the `MSEdge.exe` processes. Simply disabling the hooking brings it back to life.

As per the Citrix, Article [disable all Citrix Application Programming Interface (API) hooks on a per-application basis](https://support.citrix.com/article/CTX107825), this can either be done via the ExlcudedImages key (pre-VDA 7.9) or the UviProcessExcludes key in anything modern.

I tested both out of curiosity and they both worked perfectly fine, the `UviProcessExcludes` required a full reboot.

As a quick note, the following string already exists in a default install, so you may want to take this and simply append our new processes to the end:

Original Value: `LsaIso.exe;BioIso.exe;FsIso.exe;sppsvc.exe;vmsp.exe;`

New value: `LsaIso.exe;BioIso.exe;FsIso.exe;sppsvc.exe;vmsp.exe;brave.exe;msedge.exe;`

I am writing them with a simple computer-based Group Policy Preference below

[![GPP]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/GPP.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/GPP.png)

And upon successful run you should see your registry reflect your changes:

[![Registry]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/Registry.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/Registry.png)

Brave and Edge now launch as expected:

[![Brave Launch]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/BraveLaunch.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/BraveLaunch.png)

[![Edge Launch]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/EdgeLaunch.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/EdgeLaunch.png)

Brave uses the concept of shields to protect your browsing experience, below hitting eBay landing page alone shows 8 blocked trackers. I run Pi-Hole to protect my household from this junk and am a massive advocate for DNS based protection in the Enterprise, however it's nice to see exactly which nasty stuff is going on in the browser locally.

[![ShieldsUp]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/ShieldsUp.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/ShieldsUp.png)

Go and hit a page like ninemsn.com.au and watch your shields start going into overdrive

[![ninemsn]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/ninemsn.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/ninemsn.png)

The browsing experience in Brave (and anything protected by DNS filtering and privacy-based tracking protection extensions) is so much snappier than without.

Brave is just Chromium under the hood so the usual capabilities apply, however, there is no enterprise capability around centrally managing it at the moment, however a few things I like to do below in my own profiles

`brave://settings/getStarted` (in the URL bar)

-  Hide Brave rewards bar
-  Show the home button
-  Set your Search Engine (Duck Duck Go for me given I am on an anti-advertising rant)
-  Set your Downloads if different than default locations
-  Set Dark Mode
-  Install any extensions you may need (though hopefully, that’s limited)

[![Dark Mode]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/DarkMode.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/DarkMode.png)

## Profile Management

Both Brave and Microsoft Edge follow the same structure as Chrome for their data pathing for the most part and will both be subject to the same buildup of junk. If you are going to throw these into an environment, it’s worth keeping an eye on data sizes and managing your profiles accordingly. In the example below, I have three profiles in Brave, the default and 2 additional. There is also a system profile.

[![UserData]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/UserData.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/UserData.png)

Browsing around for a few bits and pieces and you can see that the usual “Cache” folder is going to start growing quite quickly

[![YouTube]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/YouTube.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/YouTube.png)

[![DataSize]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/DataSize.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/DataSize.png)

For those using FSLogix, add the following path to your redirections. Remember this is per profile in Brave, Chrome or Edge, so you need to be wary if you are using multiple profiles:

Brave: `<Exclude>AppData\Local\BraveSoftware\Brave-Browser\User Data\Default\Cache</Exclude>`

Edge: `<Exclude>AppData\Local\Microsoft\Edge Dev\User Data\Default\Cache</Exclude>`

[![FSLogix]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/FSLogix.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/FSLogix.png)

For Citrix UPM you will need to add the following to your **Exclusion list – directories** policy

Brave: `!ctx_localappdata!\BraveSoftware\Brave-Browser\User Data\Default\Cache`

Edge: `!ctx_localappdata!\Microsoft\Edge Dev\User Data\Default\Cache`

## System Impact

Both Brave and Microsoft Edge will throw in their own update services that you will want to deal with:

[![Services - Brave]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/Services-Brave.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/Services-Brave.png)

[![Services - Edge]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/Services-Edge.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/Services-Edge.png)

They will also both throw in the usual scheduled tasks to slap:

[![SchedTasks - Brave]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/SchedTasks-Brave.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/SchedTasks-Brave.png)

[![SchedTasks - Edge]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/SchedTasks-Edge.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/SchedTasks-Edge.png)

Interestingly, and not that I have done any level of extensive testing, but out of the three Chromium-based browsers, Microsoft Edge appears to have the lowest consumption of resources, followed by Google Chrome and then Brave.

[![TaskMgr]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/TaskMgr.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/TaskMgr.png)

## Seamless Apps

The usual rules apply for both Brave and Edge for application publishing

[![PublishedApp_Brave]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/PublishedApp_Brave.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/PublishedApp_Brave.png)

[![PublishedApp_Edge]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/PublishedApp_Edge.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/PublishedApp_Edge.png)

Both apps launch seamlessly and without any obvious issues I can see

[![PublishedApps]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/PublishedApps.png)]({{site.baseurl}}/assets/img/deploying-brave-and-microsoft-edge-dev-browsers-in-citrix-cvad-environments/PublishedApps.png)

## Summary

Whilst this is not in any way shape or form an extensive guide to deploying these browsers, hopefully, it unlocks the door to allow you some basic testing, and give you a few more options in your tool kit

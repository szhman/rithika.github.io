---
layout: post
title: "Selective Deletion of the WEM Actions Tracking Cache"
permalink: "/selective-deletion-of-the-wem-actions-tracking-cache/"
description: "Selectively resetting what WEM has actioned for the user"
categories: [Citrix, PowerShell, WEM, Windows]
redirect_from: 
    - /2018/11/28/selective-deletion-of-the-wem-actions-tracking-cache
    - /2018/11/28/selective-deletion-of-the-wem-actions-tracking-cache/
image:
  path: /assets/img/selective-deletion-of-the-wem-actions-tracking-cache/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Every action that WEM handles, and most settings that are applied in the user context are stored within the users registry hive within the following locations:

For basic Actions, Tasks, Environment and USV style settings:

> `HKEY_CURRENT_USER\SOFTWARE\VirtuAll Solutions\VirtuAll User Environment Manager\Agent\Tasks Exec Cache`

And for user based printing management settings:

> `HKEY_CURRENT_USER\SOFTWARE\VirtuAll Solutions\VirtuAll User Environment Manager\Agent\User Printers Management`

[![Action Tracking in User Hive]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/RegCache1.png)]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/RegCache1.png)

Action Tracking in User Hive
{:.figcaption}

I have [blogged previously](http://jkindon.com/2018/06/19/multi-site-and-onedrive-folder-redirection-with-wem/) about weird things that can happen for certain components in this tracking cache, such as changing USV settings, which once applied and tracked, are never applied again, and I have also seen a fair few cases on the forums and across customers where printers or applications occasionally fail to process until the Tracking items are deleted from the hive

Rather than hammer punching the entire user tracking cache (which can reset all of your RunOnce settings) I have put together a small script to help with the task of dealing with problematic WEM Actions tracking for environments utilising WEM to build out the user workspace

The script is nothing magical, it just provides a front end PowerShell interface that you can publish as a WEM application, to provide a nicer way to address specific challenges. I figure it can help support staff that don’t particularly want to have to guide users through navigating the registry

From a requirements perspective, the script is 100% PowerShell based, so if you block PowerShell for your users, you are probably not going to enjoy the script. You will also need to house the Script in a location accessible to your users

[![WEM Selective Action Tracking Reset Utility]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/Menu.png)]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/Menu.png)

WEM Selective Action Tracking Reset Utility
{:.figcaption}

In my environment below, I have simply configured a new WEM application as follows, gave it a nice icon and made it available in the WEM application list. Deal with it as you see fit – it’s a troubleshooting tool really so I wouldn’t imagine too many people dumping it into the start menu and letting users go haywire – but hey, a quick WEM refresh and you are back in action, so you can’t cause too much damage

[![WEM Selective Action Tracking Reset Utility]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/App.png)]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/App.png)

WEM Application Settings
{:.figcaption}

[![What the user gets]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/AppResult.png)]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/AppResult.png)

What the user gets
{:.figcaption}

All tracked actions and settings have been catered for, so you can delete printers, or network drives, or folder redirection settings and leave everything else intact, but if you do however want to face palm the entire tracking cache, then you can select the magical option 21…and bye bye everything until next refresh

[![Option 21 - Face Palm]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/Hammer.png)]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/Hammer.png)

Option 21 - Face Palm
{:.figcaption}

[![DeadCache]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/DeadCache.png)]({{site.baseurl}}/assets/img/selective-deletion-of-the-wem-actions-tracking-cache/DeadCache.png)

Bruised and Beaten Agent Cache - Thanks Option 21
{:.figcaption}

## Script

Code is in [GitHub](https://github.com/JamesKindon/CitrixWEMActionTrackingReset), feel free to use it, alter it, criticise or ridicule it, it’s a quick knock up to make an annoying chore a little easier on the eyes

---
layout: post
title: "AppLocker Script rules to maximise WEM logon benefits"
permalink: "/applocker-script-rules-to-maximise-wem-logon-benefits/"
description: "Killing nasty logon scripts with AppLocker to let WEM do it's job"
categories: [AppLocker, Citrix, WEM, Windows]
redirect_from: 
    - /2018/08/05/applocker-script-rules-to-maximise-wem-logon-benefits
    - /2018/08/05/applocker-script-rules-to-maximise-wem-logon-benefits/
image:
  path: /assets/img/applocker-script-rules-to-maximise-wem-logon-benefits/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

One of the many benefits of WEM, and arguably the most important one, is the crazily reduced logon times you can gain by transferring actions and environmental setup tasks that would traditionally be handled by Group Policy Preferences or logon scripts. I wont go into detail about this here, as by now we mostly know the methodology on how and why WEM is a superior way to action these tasks.

What I tend to do in engagements that allow me to either green fields build, or implement within an existing environment, is ideally wipe out everything else in the environment that can effect what I want to WEM to deliver. To do this, I have a few tasks:

1.  Block inheritance on an OU with my target Workers in them, I also hunt down any GPO that is set to enforced and find out why as this will [override blocking inheritance](https://blogs.technet.microsoft.com/grouppolicy/2009/12/18/tales-from-the-community-enforced-vs-block-inheritance/)
2.  Ensure that [Group Policy Loopback in replace mode](https://blogs.technet.microsoft.com/askds/2013/02/08/circle-back-to-loopback/) is configured so that no pesky user GPO's with nasty settings hit my environment
3.  Duplicate existing GPO's that have preferences in them (user context) and convert them into WEM actions. I do this using [Arjans PowerShell module](https://msfreaks.wordpress.com/2018/01/08/powershell-module-for-citrix-wem-part-3-environmentalsettings-and-microsoftusvsettings-from-gpo-and-much-much-more/) that we worked on over Christmas break 2017\. Once converted, I remove from the GPO so that it contains just ADMX based settings
4.  Convert any logon scripts held within GPO's and create them as [WEM External Tasks](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/actions/external-tasks.html). Again this primarily actioned with Arjans PowerShell Module, or if there is only a couple, then I just manually create the task. I then remove them the GPO Objects

At this point I am left with an OU that I control, which has policies that are either new, or copied, cleaned of preferences and scripts and reviewed for appropriateness. This is a nice place to be but there is one outstanding component: **Logon Scripts on the user account**. This one is pretty narky as often we are not just working in a Citrix only environment, and these logon scripts effect the user no matter where they log in.

Enter [AppLocker Script rules](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/applocker/script-rules-in-applocker) to the rescue.

What I have found to work so far to date, is applying an AppLocker Script rule to block ALL scripts from running against the Citrix environments. This includes the logon scripts configured on the user accounts. I add a location (typically within the netlogon share) that I want WEM to be able execute scripts from, and simply add an allow rule for AppLocker to allow Scripts to process.

[![AppLockerScript]({{site.baseurl}}/assets/img/applocker-script-rules-to-maximise-wem-logon-benefits/AppLockerScript.png)]({{site.baseurl}}/assets/img/applocker-script-rules-to-maximise-wem-logon-benefits/AppLockerScript.png)

AppLocker Script - Path Rule
{:.figcaption}

So far it works a treat, gives me what I want and ensures that by targeting only the Citrix OU's with the rules, that I don’t hurt any other environment.
---
layout: post
title: WEM Advanced Guidance Part 3 - The Leftovers. The Good, Bad, Ugly
permalink: "/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/"
description: WEM Advanced Guidance Part 3 - The Leftovers. The Good, Bad, Ugly (Historical Post)
categories: [Citrix, WEM, Windows, CVAD]
image:
  path: /assets/img/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
related_posts:
  - blog/_posts/2017-11-30-wem-advanced-guidance-part-1.md
  - blog/_posts/2018-01-04-wem-advanced-guidance-part-2-user-interaction.md
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

This is an [old post](https://blogs.mycugc.org/2018/02/08/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/) which I initially wrote with Hal Lange for CUGC, however the indexing on their site isn't great, so I have ported the content here, and scrapped out any legacy guidance. For up to date recaps 5 years on, see [WEM Advanced Guidance 2023](https://jkindon.com/wem-advanced-guidance-2023)
{:.warning}

In our last two posts, we discussed topics ranging from architectural guidance and considerations, through to how users interact with WEM in their environments and how to try and keep that experience smooth.

Initially, we had aimed for part 3 of this series to be about why you should implement WEM, and some specific use cases. However, given how rapid the take-up has been, and how many organisations are now using it and reaping the benefits, we figure that there really isn't much point in spruiking something that has already sold itself.

As such, we decided to focus on a range of "Gotcha's," considerations, and bonus fun that we have been seeing across our own deployments, the Citrix discussions forums, and our own WEM-based community chats.

## Considerations and Gotchas

Below is a list of basic considerations that we have come across in our travels to date and feel are worth mentioning:

### NOT ALL ENVIRONMENTAL SETTINGS ARE RELEVANT TO ALL OPERATING SYSTEMS

WEM, whilst awesome, has its limitations and caveats like any other technology. One such caveat is that at the time of writing, not all environmental settings apply properly to all operating systems that WEM can support. Remember that WEM's primary development phase was around the Windows 7 / 2008 R2 era, and many changes have occurred to the underlying operating systems in the more modern platforms that render some options useless in WEM. Settings for all OS's are laid out in one simple window so there is no way to know what does and doesn't work.

There are plenty of environmental settings within the WEM console that you should test thoroughly in your environment. I have instances where "hide common programs" kills the start menu in some environments and not others. I also have reports and have watched the environment do some very strange things when "process environmental settings" is ticked and they couldn't use any processing of these settings at all. This was linked to a big multi-forest multi-domain scenario that had other issues as well. No explanation to date.

This is no big deal however, as these settings can all be driven via GPO instead, and represent little to no performance difference when being applied by ADMX (remember ADMX is not the problem, GPP is when dealing with logon speed).

What we would like to see moving forward is a breakdown in the WEM console of what settings are relevant to which OS–but maybe that is simply another blog post for someone to write.

### NOT ALL UEM SUITES ARE BUILT EQUAL

I am basing this as a "gotcha" purely based on user expectations and some feedback received on the Citrix forums around what WEM does and does not do.

Let's be clear on WEM's primary objective: Its sole reason for existence is to give users access to their resources quickly, and to ensure that the user experience is optimised and protected at all times. There are some other features and bonuses that we get from WEM, but this is the reason for its existence.

**Historical Note**: It would be fair to say the solution has progressed far past its positioning below 5 years on.
{:.warning}

Further to this, WEM is provided at no additional cost to valid Citrix Entitlements. ~~~Importantly, WEM does not compete with Ivanti, Liquidware, etc. It is a small tool with giant capability, but it is not a full UEM behemoth such as those mentioned~~~. It provides configuration for HKCU configuration and machine-based CPU/Memory/IO protection, as well as some new security functionality. It optionally drives Citrix UPM, ~~~VMware Persona~~~ and Microsoft USV Settings. It complements other configuration tools (GPO etc) and that's it.

~~~If you want full UEM, and a full one-stop-shop for everything relating to a user environment, then WEM is not the answer, and does not claim to be.~~~

### WEM IS NOT A PROFILE MANAGEMENT TOOL

WEM is not a profile management tool, as such, it falls victim to the underlying considerations and requirements of whatever Profile Technology it is driving. When considering how to drive your Profile Management, you should be considering flexibility, portability (what happens if I want to move your activation point to another technology), extendibility, HA and DR scenarios.

Driving these solutions via WEM (USV, UPM, ~~Persona~~ etc) is fine, however, you need to be aware of the limitations of doing so, and architect around it, or move it to an alternative configuration point such as GPO. If you have an environment that requires significant differences in profile configurations, then driving with WEM will introduce significant complexities, however if you have a simple deployment it may well sit just fine. [Considerations for UPM](https://docs.citrix.com/en-us/profile-management/current-release/plan.html)

And, for an often more easier-to-consume set of guidance, Carl Stalhood has a good write up: [Carl Stalhood – UPM](http://www.carlstalhood.com/citrix-profile-management/)

To get into some advanced UPM multiple store configurations, you can follow Hal's guidance as well: [Hals Advanced UPM Post](http://carlwebster.com/citrix-profile-management-use-one-image-point-multiple-profile-management-stores/)

The key to successful Profile Management implementations is planning carefully and deliberately. This is even more relevant when choosing WEM as your driving point.

### DATABASE UPGRADES

When upgrading a WEM environment, to be safe, consider upgrading through each version, if only for upgrading the Database Schema itself. If you have ever come across Hal's post here you will understand the reasoning behind this recommendation. It is by no means a Citrix documented guideline, but it's worth considering this base of lessons learned in the past.

It is also recommended to disable any sort of SQL replication or mirroring prior to any sort of WEM upgrade, you can see my post [here](http://carlwebster.com/upgrading-norskale-vuem-citrix-workspace-environment-management/) on a quick [WEM Upgrade Process](https://jkindon.com/2017/12/18/wem-upgrade-process/).

### MULTIPLE CONFIGURATION SET MANAGEMENT IS CURRENTLY PAINFUL

~~~As it currently stands, the management of multiple configuration sets is pretty painful.~~~ You will see throughout our posts and comments around the place, that simplicity with WEM deployments is key. If you look back on our first article in this series, you will see the key decision points that lead to multiple configuration sets.

Keep a close eye in this area as some of our cohorts in crime have some really special tools in development, which will change the way you manage WEM as a whole.

### APPLOCKER

WEM 4.5 introduced production support for driving AppLocker Policies via WEM (WEM Security). This is great currently for VDI deployments, however it's not, at this time of writing, fit for SBC based deployments. Hopefully this is resolved in the not too distant future, as simplifying AppLocker and driving it via WEM introduces a load of flexibility and simplicity and is a great idea.

If you want to amuse and confuse yourself, enable procmon and watch WEM manipulate AppLocker registry keys in an SBC environment with multiple online users being assigned AppLocker Policies.

For all AppLocker implementations, make sure to have a plan of attack and TEST, TEST, TEST.

Like everything we have spoken about to date, try not to have multiple points of configuration for AppLocker, this can result in chaos.


### THE OVERZEALOUSNESS OF ENVIRONMENTAL LOCKDOWN

Occasionally we tend to disable things without really thinking about the impacts of what we are actually doing. It's easy enough to say for example, "remove run" with a simple tick box, however the implications of doing this can have more than meets the eye, for example:

Removal of run via WEM checkbox will inadvertently also remove the ability to access UNC paths. This will neuter not only the user experience, but an admin's ability to troubleshoot as well. This a Microsoft gotcha and not limited to only WEM/Citrix. To disable the run command from the Start Menu, you need to look at `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced\Start_ShowRun`.

Many other settings for the Start Menu and Windows Explorer can be found at `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced`. This is a more subtle and humane way to deal with setting a user experience. Here are some example keys:

**`Hidden`** – DWORD(1/2) – show hidden/don't show hidden files

**`HideFileExt`** – DWORD(0/1) Don't show/Show file extensions

**`ShowSuperHidden`** – DWORD(0/1) Hide/Don't Hide protected Operating System Files

**`StartMenuAdminTools`** – DWORD(0/1/2) Programs Menu Only/Programs Menu and Root of Start Menu/Do not display

## Bonus fun

A few nice little bits to tie things together as we move towards the end of this series, some fun and thought teasing hints below:

### SPLASH SCREEN CUSTOMISATION

Many deployments that we work in like to have the splash screen hidden from users and run operations silently behind the scenes. Some, however, like the splash screen on logon and like users being made aware that things are indeed processing.

If you fall into this boat, you can brand the splash screen nicely, and remove the default green WEM splash

[![WEM Splash Screen]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/wem-splash.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/wem-splash.png)

That's not Hal by the way, Hal is uglier and hairier (Self-Confessed)

### PROFILE CLEANSING

UPM has had its limitations in the past, particularly around removing data when you have made changes to your exclusion list, which lead to no redundant data being left on your file servers. This has been resolved in UPM 5.8 onwards, where you can now tell UPM to clean-up old data itself, however it's an all or nothing approach. WEM has also had this capability built-in for a while and offers a nice GUI to allow you to selectively target profiles for cleansing–this can be great for testing and making sure your changes don't break things and doesn't require you drive the UPM solution full time via WEM, you can simply consume the tool via a dedicated config set and profile configuration that mirrors your production.  

**NOTE** Depending on the size of your UPM store, this may take hours to clean.

[![WEM Profile Cleansing]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/wem-profile-cleansing.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/wem-profile-cleansing.png)

### OR FILTERING WITH WEM

Filters and Conditions lead to rules used for Assignments in WEM. The most common way of reading these filters and combinations of filters is with the "AND" mentality, there is a seemingly glaring hole in the OR space. I say "seemingly" as many OR scenarios can be addressed on the actual filter condition itself, rather than at the rule level.

[![WEM OR Filtering]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/wem-or-filtering.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/wem-or-filtering.png)

All Rule combinations mean AND. If you add two conditions into a rule, it means both must match, however there is nothing to say that you can't have multiple values in any condition, and for those condition types that support multi values, the multi value actually means OR, and you can then often achieve what you need.

### GO THE COMMUNITY

Being a relatively new acquisition to the Citrix stack, the community work in this space has been awesome. I would encourage anyone learning and living this technology to blog and share their findings and their challenges.

Additions to the community driven stack now consist of a [full-fledged PowerShell module](https://msfreaks.wordpress.com/2018/01/08/powershell-module-for-citrix-wem-part-3-environmentalsettings-and-microsoftusvsettings-from-gpo-and-much-much-more/) to convert your GPP and USV Settings from Group Policy to WEM modules, CSV conversions to WEM modules for bulk work with printers and mapped drives, and the ability to take an already configured start menu and convert it to WEM applications. This work by Arjan has been unbelievable, and it's the tip of what's being worked on.

### THINK OUTSIDE THE SQUARE

This feels like a nice way to round out this series, and that's with a challenge to think outside the square with WEM. There is not always an obvious way to achieve a result at first glance, but often with a bit of thinking outside the square you can get there. For example:

-  There are limitations with a registry action when trying to deal with adding Binary values, doing that natively within WEM is not possible, however nothing stops you running an external task to import the .reg file on logon.
-  There is also no native way of putting application shortcuts into a specific folder on the desktop. However, you could choose to create a directory and point it to a start menu location where your shortcuts are kept and presented.
-  ~~WEM cannot deal with logoff actions – but who's to stop you from creating an external task to run a script that creates the task on logon, with a run condition of logoff?~~

[![WEM Outside the Square]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/wem-square.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly/wem-square.png)

Not everything is simple, and not everything can actually be addressed by WEM, but a lot can and the more you can get to know the product, the more possibilities arise.

## Conclusion

This is the final part of this series, and I think we are pretty much ready to talk about something else.

Thanks for taking the time to read and we hope the series has had a positive impact on your WEM adventures to date!

James & Hal

Check out the first two parts of the series here:

-  [WEM Advanced Guidance Part 1](https://jkindon.com/wem-advanced-guidance-part-1)
-  [WEM Advanced Guidance Part 2: User Interaction](https://jkindon.com/wem-advanced-guidance-part-2-user-interaction)
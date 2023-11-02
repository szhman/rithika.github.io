---
layout: post
title: "WEM Hydration Kit"
permalink: "/wem-hydration-kit/"
description: "a jump start for Citrix WEM"
categories: [Citrix, Optimization, WEM]
redirect_from: 
    - /2017/10/07/wem-hydration-kit
    - /2017/10/07/wem-hydration-kit/
image:
  path: /assets/img/wem-hydration-kit/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

WEM has to be my favourite Citrix acquisition to date, it's simple, easy to drive and is going to play a huge part in the Citrix stack moving forward.

There are some challenges at the moment around mirroring or porting certain configurations around to different deployments, and the lack of PowerShell is a touch frustrating, but I am hoping with time that will come.

I have put together a WEM Hydration kit over on GitHub, that I will continue to update as I go along with the more common applications, and other actions that I find useful for environment control via WEM.

These configurations are all handled by the WEM export functionality, and can be directly imported into your environment for you to then assign accordingly. Some actions may require a change of path or location to suit your environment, however they are all marked in the appropriate `Inventory.txt` file.

Initial list within this release as follows, there is a master list which contains everything, as well as individual breakdowns so you can be selective for certain features/functions if required.

Simply download the kit, and import into WEM using the restore wizard in the Administration Console.

[![Restore]({{site.baseurl}}/assets/img/wem-hydration-kit/Restore.png)]({{site.baseurl}}/assets/img/wem-hydration-kit/Restore.png)

Select your actions then select your apps (or choose all)

[![Restore_Selection]({{site.baseurl}}/assets/img/wem-hydration-kit/Restore_Selection.png)]({{site.baseurl}}/assets/img/wem-hydration-kit/Restore_Selection.png)

Select Next

[![Restore_Progress]({{site.baseurl}}/assets/img/wem-hydration-kit/Restore_Progress.png)]({{site.baseurl}}/assets/img/wem-hydration-kit/Restore_Progress.png)

Once completed you are ready to assign to your users

[![Hydrated]({{site.baseurl}}/assets/img/wem-hydration-kit/Hydrated.png)]({{site.baseurl}}/assets/img/wem-hydration-kit/Hydrated.png)

Beats manually creating them...

Current kit release as below:

**Master Kit:** Contains all Applications, File System and Registry Actions to date (A detailed list is found here [Inventory List](https://github.com/JamesKindon/WEMHydrationKit/blob/master/README.md))

**IE Easy List Kit:** Contains registry and file system actions to control the IE Tracking protection utilising EasyList. You will need to customise these for your own environment

**Windows Explorer / Environmental Control Kit:** Contains some basic Windows Shell Environmental Controls such as icon sizing

**Logon Performance Kit:** Contains registry keys relating to increasing logon performance.

This is a start - please feel free to comment with additional common actions that you deploy via WEM which may help the community, and I will test and add them to the kit.

You can find the source here: [WEM Hydration Kit](https://github.com/JamesKindon/WEMHydrationKit)

Enjoy

15.11.17 Update: Note that there is an updated post with some more community input for this tool [here](http://jkindon.com/2017/11/14/wem-hydration-kit-update-community-style/) 
{:.attention}

27.11.17 Update: UPM Module Added [Here](http://jkindon.com/2017/11/27/citrix-upm-module-for-the-wem-hydration-kit/)
{:.attention}

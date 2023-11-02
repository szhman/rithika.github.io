---
layout: post
title: "Addressing Multi Session Profile Management with FSLogix Containers"
permalink: "/addressing-multi-session-profile-management-with-fslogix-containers/"
description: "Multi Session Profile Management with FSLogix Containers"
categories: [FSLogix, Profiles]
redirect_from: 
    - /2018/10/22/addressing-multi-session-profile-management-with-fslogix-containers
    - /2018/10/22/addressing-multi-session-profile-management-with-fslogix-containers/
image:
  path: /assets/img/addressing-multi-session-profile-management-with-fslogix-containers/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

I recently had the pleasure of working with a great customer on an FSLogix Profile Containers Implementation to help address some pain points they had been having with their desktop and VMware based VDI platform. The more I delved into this environment, the more I felt like it was one that would mirror many challenges that existing organisations have, and as such is a perfect use case for the FSLogix Solution.

I have written previously about this solution, what it is and how it fits together, you can read that [here](https://jkindon.com/2018/02/20/getting-to-know-fslogix-containers/).

A brief summary of their current state of play and some challenges they were having, along with how the FSLogix Solution came into play to help brings things back to the way we want environment management to be: simple and reliable

## The Existing State

Anyone who has moved from Windows 7 to Windows 10, from Physical to VDI, On-Prem to Cloud Services has at some point reached a point where "head meets wall" moments are regular. When you have done them all at the same time, those moments can and will most likely be frequently occurring. Things just aren't simple anymore, and they really should be.

[![Rage]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Rage.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Rage.png)

A few points about the existing environment below:

-  Windows 10 1709 across the board.
-  Office 365 ProPlus with services such as Exchange already migrated to Office 365.
-  OneDrive being played with in a controlled fashion.
-  Microsoft Roaming Profiles shared across physical desktops, VDI and roaming notebooks.
-  Folder Redirection (Inclusive of AppData Redirection) to try and help with application roaming issues.
-  Offline files configured for the roaming profiles and home drives for the notebook fleet.
-  Chrome with Enterprise configuration to move Chrome AppData to Microsoft AppData (Thus redirected)

Important to note is that this environment was managed well. A good testament to the guys managing this really, their policies and environmental control techniques were clean, they had just reached a point where they needed something to help stabilise, reduce complexity and standardise user experience - the amount of change with each iteration of Windows 10, Office Pro Plus, OneDrive etc is huge, so they had done exceptionally well to have the environment as clean as it was

## The Existing Challenges

To successfully implement a solution, we needed to clearly understand the existing challenges, where they came from and why they were there, as well as the requirements that would mandate a successful resolution.

[![Challenges]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Challenges.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Challenges.png)

Some of the challenges associated with the environment that we needed to address:

-  Multiple styles of VDI management. Persistent, non-persistent and a semi-persistent set of configurations due to Outlook Cache Data.
-  Microsoft Roaming Profile configurations were quite hefty. There had been years of learnings and exemptions gone into the configurations to address challenge after challenge.
-  Microsoft Roaming Profile corruptions due to the multiple different use cases and split of profiles across multiple endpoints.
-  Offline files are horrendous and always have been, yet there hasn't traditionally been a valid alternative.
-  Logon Speed for VDI was pretty nasty at times.
-  Each Image refresh in the VDI environment would introduce a reset of the VDI's that had been configured as semi-persistent for the heavy Outlook users

The requirements that would mandate a successful solution:

-  VDI environments needed to be moved back to single image delivered, non-persistent based desktops. The team was sick of managing variations simply to address cache data requirements
-  Physical Desktops and Virtual Desktops must have an identical look and feel. This was extremely important to the business
-  Office 365 Data needed to become a non-issue. There was a consensus (that I agree with) that moving services to the cloud should not be hindering desktop environments of any type
-  Laptop Users needed to have access to their data. Profile management was not so important
-  SAN Space is important. So profiles need to be streamlined where possible
-  Solution must be currently aimed at on-premise infrastructure with minimal appetite for cloud IaaS or Storage Services at this point

## The Solution

The customer had investigated themselves into what some of their options were, and FSLogix was at the forefront of most conversations given it's completely isolated from any existing vendor or product suite in play

[![FSLogix]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/FSLogix.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/FSLogix.png)

We delved into a PoC of the solution and below is the architecture we moved forward with:

-  Microsoft Roaming Profiles ditched from VDI and Physical Desktop deployments. Replaced with FSLogix Profile Containers.
-  Microsoft Roaming Profiles ditched from the Laptop Users. No Profile Management in play for these users, simply folder redirection with a view to move this data to OneDrive.
-  [FSLogix Profile Containers](https://fslogix.com/products/profile-containers) for the VDI and Physical Machines in [concurrent access mode](https://docs.fslogix.com/display/20170529/Concurrent+User+Profile+Access). We chose this model with an understanding that the first session owns a read/write session, whilst any additional sessions will simply have a read only copy of the profile available. This suited the customers' requirements perfectly fine
-  [FSLogix Office 365 Containers](https://fslogix.com/products/office-365-container) for Office 365 Data in [Per Session concurrent access mode](https://docs.fslogix.com/display/20170529/Concurrent+Office+365+Container+Access). We chose this model due to the lack of support for differencing disks when using Windows Search and Outlook Cache Mode. This model requires significantly more space, however it provides a supported model for multi session access which would be a frequent occurrence as users roam between physical and virtual environments
-  FSLogix Profiles configured to [redirect temp data to local c: drive (SetTempToLocalPath)](https://docs.fslogix.com/display/20170529/FSLogix+Profiles+Configuration+Settings). We decided on this due to the requirement to keep profiles lean. Why persist throwaway temp data if we don't need to.
-  FSLogix Profiles configured to use a decent [redirections.xml](https://docs.fslogix.com/display/20170529/Controlling+the+Content+of+the+Profile+Container) file to remove useless bloat from the profile.
-  FSLogix Profiles configured in [single user search mode](https://docs.fslogix.com/display/20170529/FSLogix+Single-user+Search) with the search index stored in the 365 container
-  [REFS File System](https://docs.fslogix.com/display/20170529/Using+ReFS+File+Shares) utilised on a Server 2012 R2 (could also be 2016) File Server behind a DFS Namespace.
-  [AppData Redirection removed](https://stealthpuppy.com/folder-redirection-2015-part-1/) (Set back to local profile location) and stored within the Profile container.
-  [Chrome App Data Redirection](https://support.google.com/chrome/a/answer/187202?hl=en) removed and left in the profile (our exclusions xml covers the obvious requirement for data bloating here).
-  Folder Redirection for the usual suspects retained. There is a view to move this to OneDrive across the board.
-  OneDrive for Business to replace Offline Files in a staged fashion over the next few months.

## Implementation Gotchas

As with any profile work, the challenges are typically the things you don't see. Scripts that were configured months ago to address a certain task, historical hacks in the registry to control behaviours related to profile management etc. As a colleague of mine has mentioned many times, and a sentiment I agree 100% with, when dealing with FSLogix Profiles, it is ***always*** environmental challenges that we need to address, not challenges with the technology capability

[![Some thinking to be had]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/ConfusedEmoji.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/ConfusedEmoji.png)

A few challenges we found and needed to work around

-  Roaming Profile Configurations were applied at the Active Directory account level. We needed to use **Computer based GPO's** to target our FSLogix based computers to [block the use of roaming profiles](https://getadmx.com/?Category=Windows_10_2016&Policy=Microsoft.Policies.UserProfiles::LocalProfile) and [prevent sync](https://getadmx.com/?Category=Windows_10_2016&Policy=Microsoft.Policies.UserProfiles::Readonlyuserprofile) in these environments. Eventually the Profile setting will be removed from the account, but this was not a small user-base, so we needed to stage the deployment
-  AppData redirection needs to be configured with a reversal setting rather than disabling
-  There were applications which did not play nicely with certain directories excluded via the redirections.xml file. With some basic [Procmon](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon) tracing we were able to identify which directories needed to be kept within the actual container. To note, this was Java based applications that were not playing nicely, so there isn't a real shock here.

## The Result

In a nutshell really:

[![Profile Contentment]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Contentment.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Contentment.png)

We were able to clearly gain the following wins:

-  Simplified profile management (or lack thereof) across the board.
-  Consistent user experience across multiple platforms, physical, virtual, it's all the same.
-  Logon Speeds reduced from figures like 3 minutes, to sub 40 seconds in some instances.
-  Admin complexity is completely gone once we ironed out the initial deployment quirks which were environmental.
-  All Office 365 based services can now be turned on without fear of *"what's going to break now"*.
-  Application behaviour is improved due to lack of AppData redirection occurring and the associated performance impact against a file server.
-  Management of things like Windows 10 Start Menu and all the other joys of Profile Roaming are simply gone. Start Menu Custom Layouts operate nicely, along with user driven changes.

## Some technical points to note

The use of [Multi Session Profile Containers](https://docs.fslogix.com/display/20170529/Concurrent+Office+365+Container+Access) is pretty cool, to visualise the process and how things fit together, I have included some screenshots from my lab

Note that in the below image I have the following configurations backing it:

-  Profile Containers in Concurrent Access Mode
-  Office 365 Containers in Concurrent Access mode with [session-based](https://docs.fslogix.com/display/20170529/Concurrent+Office+365+Container+Access) disks
-  Redirect Temp Data to local C: via FSLogix ADMX Settings
-  I have two sessions open from two different XenApp (Server 2016) Images

[![Container Files]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Containers.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Containers.png)

From an access standpoint, in the image below you can see the `Jkindon5` user has `read/writ`e access to the `RW.VHDX` file. This disk is created and accessed by the first session to access the profile and captures all changes. The next session to load the profile mounts a `read-only` copy of the `PROFILE_JKINDON5.VHDX` file. All changes are discarded

[![Open Handles]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/OpenFiles.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/OpenFiles.png)

There are also two session disks in play, both `Read-Write`. This is to capture all Office 365 data in a completely supported fashion

[![FSLogix Current Support Statement]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/FSLogix_Support.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/FSLogix_Support.png)

To back this up with some log file entries, you can clearly see below the first session checks to see if there is a RW disk to merge, if not it creates a new `RW.VHDX` and mounts it with Read Write permissions

[![Read Write Mount Operations]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Logs1.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Logs1.png)

The second session load finds the a `RW.VHDX` file and tries to merge it, it notes that the `RW.VHDX` file is open in another session and thus mounts a Read Only copy of the Profile. New Logging in the 2.9.4 release shows the temporary writable container stored in the default location of `C:\Windows\Temp`. In a provisioning server environment, you would want to be careful about the placement of these temporary disks, obviously having them on the C drive will potentially blow out your PVS write cache, luckily in release 2.9.4 you can set a registry value to move this temporary location to a different drive.

[![Read Only Mount Operations]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Logs2.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/Logs2.png)

We can see below also that my profile is stored under `c:\users\jkindon5` and local temp data is redirected to the `local_JKindon5` directory. This is deleted at logoff

[![File System]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/FileSystem.png)]({{site.baseurl}}/assets/img/addressing-multi-session-profile-management-with-fslogix-containers/FileSystem.png)

### Summary

Profile Management *should* be simple. Migration of Services to the cloud *should* be simple. VDI *should* be simple. Unfortunately, this is not always the case. The more I discuss with customers their challenges in this space, the more I am drawn towards the simplicity and reliability of the FSLogix solution.

I don't have to understand how the VMWare VDI environment fits together, nor do I need to know the differences between MCS and PVS provisioning in Citrix delivered environments, all I need to know is that I can install an agent on the image, the same as I would in any other windows environment, and we are cooking. The solution continues to grow in capability and its beauty is in how well thought out and simple it is. There is typically an easy answer for every scenario and for the cost of a cup of coffee a month per user – it's very hard to ignore.

---
layout: post
title: Citrix UPM and FSLogix Containers
permalink: "/citrix-upm-and-fslogix-containers/"
categories: [Citrix, UPM, FSLogix, Profiles, Windows, CVAD]
redirect_from: 
    - /2021/09/16/citrix-upm-and-fslogix-containers
    - /2021/09/16/citrix-upm-and-fslogix-containers/
    - /citrix-upm-and-fslogix-containers/AMP
    - /citrix-upm-and-fslogix-containers/AMP/
description: Delving into the when, why and why not of Citrix UPM and FSLogix Profile Containers
image:
  path: /assets/img/citrix-upm-and-fslogix-containers/post_default_image.jpg
sitemap: true
hide_last_modified: false
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Preface

This article was written in 2021 where the landscape was very different to what it is today (end of 2023). I am both updating this article for relevance (but keeping the initial content also), and revamping with some updated logic and comparison in a [new post here](https://jkindon.com/citrix-upm-and-fslogix-containers-2023)

## Intro

Too. Many. Times. That’s how I am starting this article. And because I love bullets, here is some clarity on that statement:

-  Too many times I have seen environments fall on their heads due to bad profile management decisions
-  Too many times have situations arisen that simply did not need to happen
-  Too many times has the term "easy" been used in place of "appropriate"
-  Too many times, tried and proven technologies are being disregarded and too many times are reputations and environments being slapped because of it

Dramatic statements but an ever-growing trend and reality.

I want to extend a Kudos to two of my most respected friends in [Shane Kleinert](https://twitter.com/shanekleinert) and [Scott Osborne](https://twitter.com/VirtualOzzy) (Ozzy) who sit down with me every week and chew the fat, share war stories on what we are seeing, and help validate my thinking with their own. Also a note of thanks to [Jarian Gibson](https://twitter.com/JarianGibson) for always being an awesome sounding board and level head.

## Preface

Back in 2019, I wrote an article [around profile management](https://jkindon.com/2019/06/12/profile-management-in-2019-what-how-why/) and what some of the options were at the time. This focused on trying to put clarity around how and when profile management is needed, along with what profile management tools and options were available.

This post is going to continue that Journey, but with another 2 years of projects and consulting covering two of my favourite contenders. Microsoft FSLogix and Citrix Profile Management. Where they fit, when to use them, and more importantly, when not to use them. To be fair, this post is realistically more about container technology vs file-based solutions, however for the most part, people will associate containers with FSLogix and Citrix UPM with the latter.

This post is purely focusing on Citrix CVAD deployments.

I will preface the following with the simple fact that I have fallen victim to my own rants here, and have deployed solutions that looking back, and knowing what I know now, I would not do again. Power of hindsight really.

I will also preface with the fact that I think both Microsoft FSLogix and Citrix Profile Management are ~~equally~~ exceptional tools in the toolbox, and both serve us very well when positioned appropriately.

~~I do not believe one solution is better than the other.~~ I don’t believe there is a default stance that should be taken, and I firmly believe that if appropriate discovery and architecture is not undertaken, then you are doing yourself, or your clients a complete injustice.

Container technology is not appropriate for every deployment. Fact.

The world moved on and CPM developed at a very rapid pace. These days, CPM is a far more robust and all round better solution across the board. FSLogix is still great, but CPM takes the cake.
{:.think_about_it}

## A Quick Recap

A very brief recap on the difference between the two solutions:

FSLogix Profiles are a container-based solution, utilising VHD or VHDX virtual disks. Its job is stick a local profile into a box, and make it portable across sessions. It is simple to deploy, requires little to no management (on paper) and seeks to provide consistency and ease of use. FSLogix offers simple 1:1 profile management options (VHD Locations) or Cloud Cache, the ability to write to two or more locations at the same time, the first technology to actually enable active-active datacentre deployments in their truest form.

FSLogix also provides an Office Container designed to bolt onto existing profile management solutions (including FSLogix Profiles) to capture Office Cache data and provide the missing link between Office 365 and non-persistent VDI and published application environments.

Citrix Profile Management is ~~primarily~~ a file-based profile solution, which has extended out into containerisation (large file handling, Outlook Cache and Search Index along with selective containerisation of data). It is very fast, very flexible and very resilient. It comes at the cost of management and fine tuning. UPM also enhances what FSLogix can deliver, an example being multi session write back to a single container.

FSLogix is now owned and provided by Microsoft. Citrix Profile Management is available to all customer who own Citrix CVAD licencing of any level.

FSLogix rose to true prominence when it became generally accessible to pretty much everyone after the Microsoft acquisition. For years I had been lucky enough to work with customers who would purchase the Office Container, and a few who would purchase the Profile solution for specific use cases. Most of my projects bolted onto Citrix UPM deployments and worked a treat.

Once the solution became generally accessible to the industry, things changed, and things changed very fast. But the question I am looking to work through here, is why?

I live heavily in the community space and am lucky enough to consult and deliver on projects globally. The following sentiments are becoming a trend, and it is concerning given it appears to be built on false understandings:

-  FSLogix Profile Containers are the default for all projects
-  Citrix Profile Management is dead
-  Containers simplify and fix all profile problems. They are “easy” and “simple” (I will address this later)

In my consulting role, I talk to many customers who are looking to, or have already begun their journey with Containers. Here are some of the common topics that arise in my conversations with them:

-  If we had known X would happen, then we wouldn’t have made the change
-  We have more outages since implementing Containers than we ever did with UPM
-  Why and how can user profiles bring my entire delivery estate to its knees?
-  That is very expensive for user profiles (expensive doesn’t just mean dollar figure, it talks to capacity, IOPS, and more importantly, outage impact)
-  Any my absolute favourite, which really gets my jaw cracking "We were never told this could happen"

Digging into the why of the decision-making process that led to choosing containers, often shows that there was actually no real thought at all, and often not even an understanding of what the solution does and why it does it.

And so herein we begin the crux of this discussion. When do we  use what and why?

## Understanding and Positioning

A couple of principals that guide me, and I think are fair to say should guide most of us:

-  It is rarely OK to justify a solution because it is easy. Not never, but rarely. Long live the unicorns where this logic carries weight (they exist!)
-  A positioning of a solution without a “why” is just bad form
-  There is always a balance between management, simplicity, user experience and cost

This post is purely discussing Citrix UPM and FSLogix Profiles and nothing else. It’s not talking about methodologies and Profile design, that’s discussed in depth already on many blogs.

I am not classifying or referring to Office Container as a profile solution. When I refer to FSLogix in a Profile context, I am referencing specifically the FSLogix Profile Container.

### FSLogix Profile Containers

[FSLogix Profile Containers](https://docs.microsoft.com/en-us/fslogix/overview) are a ridiculously powerful solution. I have been deploying them for years and have always been a huge fan. Doesn’t mean I dump them everywhere. Some trigger points for their use:

-  Windows 10 and Microsoft Office 365 are in the mix
-  Modern Apps in Windows 10 get their own special mention. Those little pricks are horrible, and FSLogix Profiles, whilst not perfect with them, go a long way to helping, particularly on Windows 10 Multi Session
-  Heavy user-based customization and application debt in AppData, with consistency being paramount. Technical debt which ties users to profiles (don’t get me going on this topic)
-  Simplicity is absolute king and the tax to achieve simplicity is understood
-  High performing storage is available to host containers
-  Capacity is not a problem and there are no issues with profiles being large
-  Highly available storage is available
-  [Azure and AWS](https://jkindon.com/2020/05/27/navigating-azure-storage-options-for-fslogix-containers/) are typically much easier to architect around given the myriad of high performing and high capacity scale solutions native to the platform. On premises, Nutanix Files, VSAN File Services, SAN based file solutions or Network Attached Storage devices are ideal. Windows File Servers offer a range of High Availability solutions
-  Simple user consumption patterns, ideally single session deployments with limited double hop scenarios
-  You don’t want to manage a profile solution with inclusions, exclusions, mirroring etc. and are willing to pay for that privilege in capacity
-  You do not need to be crossing back and forth between different profile versions (less problematic in modern Windows 10 but who knows where that will go). We all remember v2, v5, v6 etc. profile versions
-  Environments require [active-active datacentre access](https://jkindon.com/2019/10/15/designing-profile-management-with-active-active-resource-locations/). This triggers cloud cache, but is not a hard and fast FSLogix trigger any longer

### Citrix Profile Management

[Citrix UPM](https://docs.citrix.com/en-us/profile-management/current-release.html) offers a massive amount of capability and has for some reason become the poor cousin or an afterthought in many environments where it is 100% the correct choice. Some trigger points I work with:

-  ~~Any Citrix deployment that is not using Windows 10 Modern Apps~~
-  ~~An adequate level of technical expertise exists to adapt the solution as requirements change~~
-  No technical debt associated with profile dependency. Profiles can (and will need to) be reset with any file-based solution. This is a nature of roaming file-based solutions unfortunately
-  If an environment uses OneDrive, then UPM is still an option for profiles~~however, must be combined with FSLogix Office Container~~
-  Environments that use a combination of published applications, desktops and double hope scenarios. These are complex use patterns, UPM handles them well and in a very lean fashion
-  High performing storage is not readily accessible. Performance is still a consideration, but less user impacting than FSLogix on that same underperforming storage
-  Storage capacity is a challenge. UPM profiles can be configured and streamlined within an inch of their life
-  Storage backend is not highly available. UPM can sustain the loss of a file server (in most scenarios) without bringing users to their knees
-  Advanced requirements including selective containerisation of data
-  Cross platform/OS profile handling is paramount
-  Integration with service desk tooling such as Citrix Director/Monitor is important

And here is a bonus one that seems to be forgotten but is critical:

-  The loss of profile storage is not an acceptable reason to bring an entire environment to its knees

It is important to note, that it is OK to use a combination of both Profile Management solutions. The right tool for the right scenario.

Citrix enhanced it's Container capability to the point where there isn't really a specifc trigger to not use it any more.
{:.think_about_it}

## Assumptions Rebuked, Myths Defunct and Lessons Learned

Here are some points to address the common items I observe, and the implementations I have been brought in to assess:

-  Microsoft Exchange Cached Mode and OneDrive Data
    -  If dealing with Exchange Cached mode and/or OneDrive data, you are going to need a container. The FSLogix Office Container is a perfect solution to this challenge, and at the time of writing, is the only supported way to deal with OneDrive. Ensure the removal of orphaned OST files are configured [RemoveOrphanedOSTFilesOnLogoff](https://docs.microsoft.com/en-us/fslogix/profile-container-configuration-reference#removeorphanedostfilesonlogoff)
    -  Consider leveraging the FSLogix Policy [Enable Cached Exchange mode on successful container attach](https://docs.microsoft.com/en-us/fslogix/profile-container-configuration-reference#outlookcachedmode) this will ensure that if the container fails to mount, outlook sessions will operate in online mode, and not try to download cache data to an undesirable location. Ensure that no other policies control cached mode
    -  OneDrive does not support multiple simultaneous connections / multiple concurrent connections, using the same profile, under any circumstances
-  Customers and their love of replicating Profile Containers
    -  If determined to do so, it’s not a simple ask unless using 3rd party tools like bvckup2 or moving to Cloud Cache. Is this really still requirement in modern environments?
-  Belief: FSLogix Cloud Cache is a full-fledged redundancy solution allowing for maintenance on File Servers
    -  FSLogix Cloud Cache is designed to sustain short term loss of a file server
    -  It is not there to provide patching windows in the middle of the day, and yes, it is a big deal if you have a long-term outage of a Cloud Cache location. Data will queue on the VDA, and when that file server comes back, it will bulk flush to that file server and annihilate it IOPS wise
    -  Cloud Cache has a completely different IOPS profile than normal FSLogix deployments. Understand where those hits are and their impact
-  Citrix UPM and FSLogix Profile Containers have very different failure profiles as it relates to user impact
    -  If you lose your file server with mounted containers, you are stuffed (Cloud Cache mitigates this somewhat)
    -  User sessions will completely die, and recovery can become a nightmare with the loss of file services hosting containers
    -  Citrix UPM based profiles (if you aren’t using active write back), will for the most part keep going. The impact is shown on logoff where data cannot write back
    -  As a side note, if you are using active write back with Citrix UPM, you are likely slapping your storage around and this can very quickly impact user experience
-  It’s always storage. FSLogix failures are, 99% of the time, storage related. The other 1% is probably DNS
    -  Customers ran out of capacity because they forgot to monitor
    -  The backend storage simply is not appropriate to handle the IOPS requirements (and this will change when moving to Cloud Cache!)
    -  There was an outage on the storage
    -  Antivirus is misconfigured, and ultimately, yep – impacting storage
-  FSLogix was designed to be hands off and simple in management, but the cost was always capacity. Most organizations/consultants aren’t thinking about this when they switch, and suddenly managing FSLogix Containers can become more complex than UPM. Don’t believe me? Implemented any of the below?
    -  [Shrink scripts](https://github.com/FSLogix/Invoke-FslShrinkDisk) to deal with white space (whilst these are amazing, they are not supported). Citrix UPM cleans up after itself when you make changes
    -  FSLogix redirections xml management is not designed to be used the way people are now using them and are arguably more complex to manage than Citrix UPM policies
    -  Having to [prune profiles to keep capacity down?](https://jkindon.com/2021/09/16/citrix-upm-and-fslogix-containers/)
-  Recovering from failure is a management nightmare. Those we have gone through it, will know what I mean
    -  Any container solution will burn you when you have an outage. Make no mistake, it will burn, and the solution will be questioned heavily. I have seen it many times. [Architect and design for this accordingly](https://jkindon.com/2019/08/26/architecting-for-fslogix-containers-high-availability/)
    -  [Mode 3 disks](https://docs.microsoft.com/en-us/fslogix/profile-container-configuration-reference#profiletype) (attempt for read-write, fallback to RO) have even worse failure profiles and should be avoided unless there is a specific need to use them
-  FSLogix Profiles are faster than UPM profiles
    -  This is not 100% true. They are more consistent as they grow and scale, but they are only faster if UPM has been misconfigured or not managed
    -  My environments with fastest logons use UPM with profile streaming and are lean

Understood, designed, implemented and supported appropriately, FSLogix Profiles are awesome. Without full consideration, customers will burn.

In all, are FSLogix Profiles less management and a simpler solution? Yes, if you follow the design principals of the solution, no if you are trying to make them more like your old mate Citrix UPM profiles. If doing this, surely the ***why*** questioning must start being asked?

Most of this still relevent in 2023, but keep in mind, Container impacts are relevant for both technologies
{:.think_about_it}

## The Progression of Citrix UPM

I want to take a few minutes here to bring some attention back to the capability of the Citrix UPM solution and the enhancements that the team have been quietly making over the years. Like the [Citrix MCS development](https://jkindon.com/2021/07/08/the-evolution-of-citrix-machine-creation-services-with-microsoft-azure/), the solution progresses but the marketing of those capabilities is lacking. Let’s just dive into a few of the cool features that will surely raise a few eyebrows for those that claim it is dead.

| Feature | Detail |
| :--- | :--- |
| [Support for file-level inclusion and exclusion for the profile container](https://docs.citrix.com/en-us/profile-management/current-release/configure/citrix-profile-management-profile-container.html#optional-include-and-exclude-folders-and-files) | Previously, you could configure inclusion and exclusion for the profile container only at the folder level. You can now do that at the file level. This enhancement gives you more granular control over profile synchronization |
| [Support for specifying the storage path for VHDX files](https://docs.citrix.com/en-us/profile-management/current-release/configure/specify-network-location-for-vhdx-files.html) | By default, VHDX files are stored in the user store. You can now specify a separate path to store them |
| [Support for using wildcards in folder names when configuring inclusion and exclusion](https://docs.citrix.com/en-us/profile-management/current-release/configure/include-and-exclude-items/overview.html) | When configuring inclusion and exclusion for the user store and for the profile container, you can now use wildcards in folder names. |
| Day 1 support for Windows Server 2022 | Out of the box, ready to go |
| [Replicate user stores](https://docs.citrix.com/en-us/profile-management/current-release/configure/replicate-user-stores.html) | Replicate a user store to multiple paths upon each logon and logoff. This is extremely beneficial for multi datacenter deployments and active-active deployments (similar to a cloud cache methodology). In a normal scenario, if both file stores are healthy, UPM will do a differential sync to both locations. Should a file store be out of date, UPM will perform a full sync to bring the data back into line |
| [Accelerated folder mirroring](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/policies/reference/profile-management/file-system/synchronization-policy-settings.html#accelerate-folder-mirroring) | With both the Accelerate folder mirroring and the Folders to mirror policies enabled, Profile Management stores mirrored folders on a VHDX-based virtual disk. Selective Containerization of data |
| [Local caching for Citrix Profile Management profile containers](https://docs.citrix.com/en-us/profile-management/current-release/configure/citrix-profile-management-profile-container.html#local-caching-for-profile-containers) | Each local profile serves as a local cache of its Citrix Profile Management profile container. This feature is designed to cater for loss of network connectivity to the container store |
| [Multi-session write-back support](https://docs.citrix.com/en-us/profile-management/current-release/configure/enable-multi-session-write-back-for-profile-containers.html) for [Citrix Profile Management profile containers](https://docs.citrix.com/en-us/profile-management/current-release/configure/citrix-profile-management-profile-container.html#put-an-entire-user-profile-in-its-profile-container) (yes, Citrix can, should you so choose, place an entire profile into a Container) | Multi-session write-back support for Citrix Profile Management profile containers and FSLogix Profile Containers. Not even FSLogix can do this natively – two sessions writing back to the same profile at the same time…
| [Profile streaming](https://docs.citrix.com/en-us/profile-management/current-release/configure/stream-profiles.html) for folders | Folders are fetched only when they are being accessed |
| [Large file handling](https://docs.citrix.com/en-us/profile-management/current-release/configure/to-enable-large-file-handling.html) | Redirect large files to the user store. This option eliminates the need to synchronize those files over the network. This will reduce logon times but will write back to the storage backend. Less shite than AppData redirection though |
| [Application-based profile handling](https://docs.citrix.com/en-us/profile-management/current-release/configure/cross-platform-settings/create-definition-files.html) | Only the settings defined in the definition file are synchronized. Uses UEV templates and allows roaming of specific application requirements. Again, we come back to the need for full user profiles – not every environment needs them |

I recall a chat I had with the profile management team a while back, where I asked if they had concerns over the long-term direction of the Citrix Profile Management solution. Their response, which I am paraphrasing slightly, but which I now completely understand was simply "*No. Citrix believes that customers are nowhere near as simple as they often think, and UPM offers, or will offer moving forward, a solution for almost every scenario*". They have even enhanced the capability of the FSLogix solution.

Ah to be where we are now. Looking back, it turns out the team was 100% spot on as we watch a return to the CPM fold occur across the globe. These capabilities were what was introduced at the time of writing. Check the [tracking post for everything else](https://jkindon.com/the-evolution-of-citrix-profile-management/)
{:.think_about_it}

## Summary

~~If you were looking for a "which is better" article, you came to the wrong place. There is no better solution between the two. One is simple and serves a simple purpose. One is flexible, robust and continues to be developed heavily for a myriad of different use cases.~~

I will continue to use FSLogix Profiles under the right conditions, because it does what it does extremely well. I will also continue to rant at those that have decided to ignore alternative or pre-existing tooling and continue to fight the fight on where profiles are even needed at all (I am looking at you health sector – long live mandatory profiles).

I will also admit openly that I am trending back towards Citrix Profile Management being a more appropriate choice in many environments~~,with FSLogix Office Container filling the gaps and providing the M365 enablement piece~~. I am also not at all scared to be suggesting that you can use both solutions in the same customer to address their requirements.

Profile Management (where profile management is required), is such a critical component that it deserves consideration and respect. Any consultant, architect or admin that does not give it that, is doing half a job. Understand what you are positioning and both the short and long-term impacts that those choices have on customers. There is no default.

I am changing my stance based on where we are now. CPM is clearly better.
{:.think_about_it}
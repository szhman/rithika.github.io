---
layout: post
title: WEM Advanced Guidance – Part 1
permalink: "/wem-advanced-guidance-part-1/"
description: WEM Advanced Guidance – Part 1 (Historical Post)
categories: [Citrix, WEM, Windows, CVAD]
image:
  path: /assets/img/wem-advanced-guidance-part-1/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
related_posts:
  - blog/_posts/2018-01-04-wem-advanced-guidance-part-2-user-interaction.md
  - blog/_posts/2018-02-08-wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly.md
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

This is an [old post](https://blogs.mycugc.org/2017/11/30/wem-advanced-guidance-part-1/) which I initially wrote with Hal Lange for CUGC, however the indexing on their site isn't great, so I have ported the content here, and scrapped out any legacy guidance. For up to date recaps 5 years on, see [WEM Advanced Guidance 2023](https://jkindon.com/wem-advanced-guidance-2023)
{:.warning}

Citrix WEM is starting to get some serious traction, and so it should, as it is a fantastic offering available to most customers at no cost. I have spent some time with the WEM Jedi himself, Hal Lange, and together we have started a blog series based on our experiences with WEM to date. This is a joint series and I have been plucking Hal's brain for a while now, so this is great to have his insight into solutions!

We are going to break this series of posts into 3 parts. Part 1 will focus on some guidance around best practices we are finding in the field, as well as input from some of the guys who have been dealing with WEM for a long time. Part 2 will focus around the user experience portion of WEM and how it interacts with the users within their sessions, and the third? We will figure out as we go. The plan is to demonstrate a set of real-life scenarios on where WEM has had a big impact on the environments.

This is not a how-to series, plenty of great guys in the community have already posted how-to links (and I will reference them accordingly throughout this series). It is aimed as a "considerations and architectural" style series.

The usual suspects have fantastic resources available for getting started with Workspace Environment Management:

[Christiaan Brinkhoff](https://www.christiaanbrinkhoff.com/2017/06/09/how-to-configure-citrix-workspace-environment-management43-for-xenapp-or-xendesktop/), [Carl Stalhood](http://www.carlstalhood.com/workspace-environment-manager/), [George Spiers](http://www.jgspiers.com/citrix-workspace-environment-manager/).

## CONFIGURATION SETS

Let's start with the highest level in the WEM environment – the Configuration Set. This is your configuration boundary for computer settings. WEM being focused on user environment control, it feels a little strange to be talking about the computer side of things, however there are a few key components that are computer based within WEM.

**Historical Note**: WEM is far more than user based now and VMware Persona management was removed from the product set. Optimization is also now dynamic.
{:.warning}

[![WEM Process Model]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-model.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-model.png)

-  Performance Management/Optimization Setting
-  Profile Management – UPM, ~~Persona~~, USV, etc.
-  Application Security Profiles (AppLocker)

There are a few things that would dictate the use of a different configuration set for your WEM deployments:

-  ~~Different machine sizing, which, in turn will mandate different performance optimization configurations~~
-  Different UPM requirements and USV requirements. Some environments may not be able to implement single namespace configurations for these services and as such a separate config set may be required, though depending on the environment, we can make changes to UPM configurations to allow for multi-site single configuration in certain scenarios
-  Multi Region requirements may dictate configuration set divisions

**Historical Note**: This is all fixed now. Import/Export is now simple.
{:.warning}

The biggest challenge with multiple configuration sets now, is the ~~lack of ability to synchronise assignments, rules and filters~~. You can export pretty much everything in WEM, ~~however the assignments portion is a right pain as it stands. I have tried all sorts of SQL manipulation to try and synchronise these, and I made some progress with filters and rules, but assignments were something I simply couldn't get past.~~

## CONFIGURATION POINTS

**Historical Note**: Significant changes to the product impact the below statements
{:.warning}

WEM provides an alternative way to drive some of the common components we are used to dealing with in every day deployments, commonly driven by Group Policy, and services such as

-  Citrix Profile Management
-  Microsoft User State Virtualization (Folder Redirection, Roaming Profiles)
-  ~~VMWare Persona~~

[![WEM Environmental Settings]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-environmental.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-environmental.png)

The way it does this is effectively no different than Group Policy; the same registry keys are populated, and the same functions occur. Given the nature of these items, it is critical that you aim to drive these functions from one place only, and do not mix and match delivery mechanisms.

The same can be said around all environmental settings. Try and drive in one solution as much as you can. This is going to become even more important once you start implementing things like WEM Security Policies (AppLocker) when it's released to production.

If you are going to drive these technologies via WEM, do not allow them to be configured by any other means. Choose a delivery mechanism and stick with it. There is no harm in combining different solutions to drive different components, but don't cross the delivery of these components with different mechanisms. Clear as mud?

## FEATURE AND ACTION PROCESSING

WEM offers a substantial amount of user and system-based settings, many you will use, however, many you may not. At its bare basics, the idea of this solution is to optimize your environment from a user experience perspective. Everything it does is focused on user experience as the priority.

WEM will attempt to process everything you enable, regardless of if there is anything to apply. Think around actions, if you don't have any variables to assign to your users, WEM will still go through the process of looking to see if there are any. Same goes for Virtual Drives, and any other action you could apply. If there is nothing to apply, don't enable the processing of these actions.

[![WEM Agent Actions]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-actions.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-actions.png)

This will stop WEM wasting time looking for things that will never be there.

## SYSTEM OPTIMIZATION

**Historical Note**: Updated settings, capabilities and guidance can be found on a more [modern set of guidance 5 years on](https://jkindon.com/wem-advanced-guidance-2023)
{:.warning}

System optimization for WEM is one of two core reasons for implementing the solution. Given the amount of performance enhancements that it can do with Memory Optimization, CPU Optimization and IO Management, it is a no-brainer to simply dump WEM into an existing environment, even just to get the gains from this alone. Density figures go up, server resource requirements go down, and it's all using standard windows magic to make it happen (no weird 3rd party tools).

However, there is a cost, and that cost needs to be managed and considered, and this is one of the big kickers around how you distribute your configuration sets in relation to machine sizing.

WEM CPU Management is amazing. Hands down, turn it on, and watch the rewards IF you tune it correctly. ~~You need to tell WEM how many CPUs you have, and this is unique to each configuration set – so if you have a VDI and an SBC environment, you will no doubt have more CPUs defined for your servers than you do for your desktops, and thus, you will need two configuration sets to effective management and optimize CPU performance.~~

~~Some basic sizing guidance offered by Hal, is to take 100% and divide it by the number of CPUs then subtract 1. This will keep a single threaded process from killing one CPU.~~

~~For Example: CPU Usage Limit = **(100% / )  – 1**~~

~~For a 4 core XenApp Server: **(100%/4) -1 = 24%**~~

~~For a 2 Core VDI Machine: **(100%/2)-1 = 49%**~~

[![WEM CPU Optimization]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-cpu.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-cpu.png)

Don't forget there is the ability to identify and exempt processes from CPU management. This is commonly useful for process such as Skype for Business, where you don't really want to be capping or getting in the way of what it is doing – I may be a touch over cautious here, but things like this standout to me as "precious" processes and I don't want them touched.

Across the projects we have worked on, we have yet to find a reason in any environment to use CPU Affinity.

CPU clamping, however, can be quite beneficial in a scenario where a bad programmer writes an application that, once launched, would take all the CPU on the box. By clamping it to say, 10%, we can keep the application from destroying the environment until said programmer could fix their mistake.

Do not simply enable CPU management and expect miracles across your different-sized environments without telling WEM what it is dealing with. Size it right and split it out appropriately, and the effects are brilliant.

Add Memory management to the mix here and there is another set of considerations. Memory Management (Working Set Optimisation) basically forces paging to disk for processes that are under the idle state limit for more than the idle sample time This is the default behaviour of windows, however, it does this at a far more aggressive schedule (you can of course customise), and by doing that, there is a direct impact on your page file. If you have ever watched this with a tool such as ControlUp, you will watch memory drop significantly, however your page file goes through the roof. And then we must start thinking about storage.

[![WEM Memory Optimization]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-memory.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-memory.png)

There is also the decision on when to use Memory Management. In SBC environments, Working Set Optimization is an absolute no-brainer. The more people we can get on a server, the better bang for buck we are sitting at, and if we can keep users happy performance-wise, then it's a win. VDI, on the other hand, poses a different set of considerations. Your users have dedicated virtual machines, and they have dedicated resources available to them. Is the cost of paging to disk worth the gains of freeing memory on your VDI machines if they are not constrained? In some of the environments I am working with, I find that memory management is better left off in VDI environments and the cost of memory is cheaper than the cost of disk, but this may will change in your specific environment and will directly relate to what applications you run.

There are many environments, particularly those running PVS, where the applications rarely need all the RAM that gets paged out, hence only the applicable parts of the application stay in RAM, and this is where working set optimization comes into its own and can be extremely beneficial. Ideally you would model WEM in a POC environment and monitor to optimize for your specific needs.

It is worth reducing the default Idle Sample time to something like 10 minutes (default is 30).

In the same way that we can exempt processes from CPU management, we can exempt processes from Memory Management – I have yet to see a requirement for this as WEM will only accelerate what windows would do, and won't do anything unless idle criteria is met.

IOPS tuning is probably the most all-over-the-shop consideration. This will be 100% relative to your environment, however there are some basic processes that should be de-prioritised from an IO perspective. Pushing this down the tree so they can't get in the way of everything else is pretty cool. You may have the reverse situations, where you want specific processes prioritised, all can be controlled by WEM and should be considered against your application set.

Some good examples of these might be processes such as TrustedInstaller, MSSecEs (Security Essentials), AV process, SCCM or any ESD Solution or and any other known High disk utilization tools.

[![WEM Process Optimization]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-processes.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-processes.png)

Finally, enable Fast Logoff, it ditches the user connection and handles the logoff behind the scenes without users having to watch.

## SBC TUNING

There are inbuilt SBC tuning functions that can be provided, based on many of the settings we would have had to enable via registry keys – the old tasks (any Citrix guy will know these) like WaitToKillAppTimeouts, etc. These are great to now have available as simple "on/off" options, however it's important to keep up with recommendations and changes in protocols and behaviours from Citrix. A prime example of this is the "Disable Drag Full Windows" Guidance which completely changed with the introduction of Thinwire – this one slipped by me big time and it wasn't until I read Dan Feller's article here that my brain went "snap."

My Point – research and only enable what you specifically require.

[![WEM SBC Tuning]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-sbc.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-sbc.png)

## BROKER PLACEMENT

There hasn't been a huge amount of guidance communicated around the ideal architecture for "Large" WEM deployments and, over time, I am sure there will be official guidance based on what Citrix sees and hears on the ground, however there are some basic guiding principles to think about when architecting these things.

-  WEM is highly resilient, and does not really have an "Active" requirement on any of its roles. There is a cache of the DB held on the WEM broker/s, and there is another cache stored on the WEM agent itself. This means that should you drop a database, you keep going. If you drop a WEM broker, you keep going, you are only limited to not being able to make changes until you restore services.
-  Simple is good. Unless there is reason to break your deployment into multiple individual deployments, then probably don't. Configuration sets are a great boundary for dividing things up within a Single WEM deployment, and you can delegate access to different administrators accordingly.
-  I like to keep a broker alongside my Citrix Controllers, and often I will co-locate them (depending on the deployment size). Citrix has some published guidance on how many connections each broker can deal with, and it's large, keeping a site as site makes sense to me from back in my Active Directory days, and if you are deploying a controller, then a WEM broker is logical. The current magic number is 5000 user connections per broker. If you have 7500 users, you would need 2 brokers.
-  Consider [load balancing](https://www.revord.net/2016/10/11/load-balancing-citrix-workspace-environment-manager/) your brokers if you have a large amount of connections – inbuilt resilience is one thing, but it's always nice to have a second node there for HA.

## CACHE REDIRECTION

**Historical Note**: This entire section has been removed given the change in the WEM landscape, however is captured for historical reference and accuracy at the time of release
{:.warning}

~~Anyone who deals with PVS is familiar with the requirement of redirecting certain application requirements to a cache/persistent disk, SCCM, Event logs, definition files etc. WEM can support this also, and it's common to see the WEM Cache moved to the persistent drive, and this is documented over the web in a few different places.~~

~~There is a countering argument to this requirement that says "don't". We don't do it with MCS, and we are seeing some interesting problems raising their heads from time to time relating to redirected cache. There is a growing thought process that redirecting this cache via PVS may be a touch redundant, and simply implementing the refresh-cache start up script on the servers will suffice. The amount of data transferred is so small that there is next to no impact on the environment.~~

~~Here is some direct commentary from Hal on why redirecting the WEM cache may be more problematic than beneficial:~~

~~Top reasons to not have a redirected WEM Cache:~~

-  ~~Client settings tend not to change.~~
-  ~~Machine setting tend not to change – What I mean by this, is all the things that get set to the machine (UPM, Performance, Site) do not change, and need to be backed into the image so they start at bootup~~
-  ~~Performance data is fresh for every user. One day the machine could be hit really hard by Excel and has that throttled (and a record of this recorded in the cache). The next day it could be PowerPoint, the next day Internet Explorer, the next Chrome. Why would I want my users to have a history of Excel being the bad apple and have it automatically throttled before the system has even analysed whether it's a bad app or not since boot.~~
-  ~~Prior to the Active Directory object integration for Configuration Set assignment (WEM 4.2 onwards), redirecting and persisting the cache would keep track of site assignment and as such tries to connect to that site first. This meant that dynamically changing configuration sets for different environmental settings was not as smooth as it should be. This is not as huge a concern in the later versions of WEM.~~
-  ~~I have found no performance reasons to have a static cache.~~
-  ~~The user will refresh their settings from the Broker at login.~~

~~Have a think about this per your environment. Both deployment models will work, however it's important to note that you do not have to redirect the cache in PVS and there may well be times when it makes more sense not to.~~

## WEM LOGGING/AUDITING/RBAC

As with any good system you manage, it is important to be aware of changes made in the environment, and delegate permissions to the environment at the correct level. WEM supports the usual requirements around configuration management, with full auditing of administrative actions and changes, along with a strong delegated access model (RBAC) to segment your management roles. You can assign and delegate permissions at either global level, encompassing all your configuration sets, or you can drill down and delegate against individual sets and roles within those sets.

[![WEM RBAC]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-rbac.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-1/wem-rbac.png)

## SQL

WEM is back-ended by a traditional SQL database, and such it would be remiss not to talk about sizing and HA.

It is important to note that the WEM solution uses stored procedures on the SQL Server and the amount of RAM allocated to the server is important for performance. Generally, WEM recommends reserving 1GB of RAM per 1,000 users deployed.

Some basic guidance is to size the virtual machine with the following calculations:

-  RAM = 1.5 GB for system + (1.5 GB SQL + 1 GB / 1,000 users) for the instance
-  CPU = 2 vCPUs for system + 1 vCPU for every 2 Brokers
-  Disk = 1 GB per 10,000 users per year + 10 MB per WEM site for DB

From a HA perspective, WEM supports all common SQL HA techniques including Always On Availability Groups

Importantly, if you are using SQL Always On, there are a couple of points to be aware of:

-  You will need to set the VUEMUser Password due to a bug in the Database. You will need to know this password in the event that you are prompted for it when syncing the Database.
-  Always remove the Database from the Availability Group before performing an upgrade. This is critical.

## SUMMARY

This has been a fun piece to start on, and I am stoked and thankful for Hal's input and knowledge – hopefully it helps with some understanding of how and where things fit in the World of WEM, and that you can start to reap the rewards of what this solution brings to the table.

---
layout: post
title: "The not-so-hidden tax of granular group based application presentation with Citrix WEM "
permalink: "/not-so-hidden-tax-citrix-wem/"
categories: [Citrix, Profiles, Windows, CVAD, WEM]
description: The impacts of a high number of Active Directory Group assignments in WEM
image:
  path: /assets/img/not-so-hidden-tax-citrix-wem/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

This one has been sitting on my list of things to post about for a while, however, time hasn’t been kind. This is my last week in the consulting world, so figured I would write this down and share the lessons before they get lost.

This post is about the cost of group-to-application deployment methodology as it relates to Citrix WEM, and some mitigation techniques.

## Scenario Context

For context, the environment we are discussing operates in a critical vertical. It is in health and as such deals with critical use cases all day every day. The environment was architected well from the ground up, with Nutanix AHV chosen for the underlying platform. It was sized for a workload that of course changed over time, however, its core sizing was done well and accurately. The environment utilises Shared Hosted Desktops on Windows Server 2016 with CVAD 1912 LTSR. The environment consists of multiple Active Directory Forests.

When I finished the initial build of the environment, we had logon times consistently under 10 seconds. My fastest recorded logon was 7 seconds end to end. This was using HTML 5 receiver, Citrix WEM, minimal GPP, Citrix UPM for profiles and for the most part, a giant Nutanix cluster to myself - so there were always going to be changes to that result, but a nice baseline to show what could be achieved.

Fast forward 2.5 years, logon times are up to almost 9 minutes in some instances, the clusters are running at 100% CPU usage, and the business is hurting. Citrix UPM had been replaced with FSLogix Containers, WEM had been used to deploy application shortcuts and handle some basic environment management components (Reg keys, file copies etc), however many items had been pushed out via GPP due to familiarity (Drive Maps, Reg Keys) and a lack of confidence in WEM, typically the feeling that not everything was consistently delivered when added to WEM - not really a shock as we delve deeper.

We have ControlUp in the environment, which helped us identify a few nasty issues chewing into the CPU. The top three contenders slapping the environment were:

-  Citrix WEM Agent. Its impact was constantly high, and its processing time was extremely long
-  Cylance AV. Cylance appeared to be upset with WEM, when WEM was high, Cylance was high
-  Microsoft WMI services. This is likely due to the number of FSLogix Containers being mounted per server. At the time the marvellous Guy Leech had not yet released his [WMI tracking SBAs for ControlUp](https://www.controlup.com/script-library-posts/show-local-wmi-activity/)

## WEM Delivery

Here is a list of what WEM currently delivered by the time I got in to start the analysis and hunt for the root cause:

-  Web shortcuts across 4 different browsers - Internet Explorer, Google Chrome, Mozilla Firefox and Chrome Portable (hundreds of these shortcuts defined and assigned). These shortcuts were presented in the users' start menu
-  RDP Shortcuts for RDS hosted applications (single RDP files). These file copies were typically copied from a local C Drive location into the users' profile to then populate their start menu
-  Citrix Published Application shortcuts using WEM StoreFront integration, again all shortcuts added to the user start menu
-  Application shortcuts for local applications installed into the image, you guessed it, added to the start menu
-  Registry Items for misc settings - some duplicated with GPP delivered settings

At the start of this assessment/remediation piece:

-  WEM included of over 600 defined Active Directory Groups
-  The logic of the environment was to create an Active Directory Group for every single defined Application - be it a web shortcut, an RDP shortcut or a published application
-  A single user account a member of nothing other than the Domain Users group resulted in a membership of 29 groups via nested groups across 2 domains. 6 of these groups aligned to groups configured in WEM with action assignments
-  Adding that same user account to the Active Directory Group which provided access to the published desktop, resulted in an expanded 157 group memberships and 70 assigned groups in WEM with matched action assignments

Let's pause on that for a second. I'm going to stick the below in a warning box to make a point:

Being a member of the Domain Users group resulted in **29 group memberships**. Being granted access to a desktop resulted in **157 group memberships**. 70 of those groups with assignments in WEM.
{:.warning}

That means for every user, regardless of their role, they will, at a minimum, have **70 Groups that WEM needs to process**. 70.
{:.warning}

Over the space of 5 weeks, the defined Active Directory Group count grew to over 620 Groups. The problem was compounded weekly.

Basic testing identified the following disturbing patterns, all tracked by reviewing WEM agent logs:

-  A user with a simple Domain Users group membership resulted in a **2 second** WEM processing time at logon with no desktop wait time recorded. This was with no profile management included
-  The same user with an FSLogix Container added resulted in a consistent **5 second** WEM processing time at logon with no desktop wait time recorded. A WEM refresh would typically take **2 seconds** to complete
-  The same user added to a desktop publishing group (which as mentioned above resulted in **70** group hits in WEM) started to get messy
    -  Logon 1 with an FSLogix Profile = **57 seconds**
    -  A WEM refresh = **47 seconds**
    -  Logon 2 with an FSLogix Profile = **51 seconds**, likely due to run once tasks are removed from the processing
    -  A WEM refresh = **44 seconds**
    -  Additional logons resulted in anything from **42 seconds to 50 seconds** in WEM processing
-  Every time the WEM agent processed, the CPU would get spanked with WEM trying to look at Group Memberships across two domains, then process all hit groups trying to understand what filters and conditions applied, and then eventually execute that end state configuration for the user
-  25-30 users per session host, all hitting hundreds of group assignments would flatline the server almost full time (logons + background WEM refreshing)

My user account was a simple test, production users would have some experiencing **5-6 minutes** of WEM processing time consistently. Some had desktop load delays of over **5 minutes**. When I say some, I mean thousands of user accounts impacted.

It's not easy to undo years of compounding pain, and it is very easy from a consulting standpoint to point a finger and say this is silly. However, application rationalisation projects are not quick ones, working through user personas and understanding who needs what are long-winded engagements often take months if not years to define and then implement. Change is hard and fear of impacting an already negative user experience often reigns supreme, resulting in tactical approaches to ease the pain rather than strategic ones to address the cause.

## Remediation

So here is what we did to tactically address the pain:

-  Using Arjans [PowerShell modules](https://www.powershellgallery.com/packages/Citrix.WEMSDK/1912.0.11/Content/Citrix.WEMSDK.psd1), we were able to export all application definitions from WEM into a CSV file so we had the data to replace the delivery mechanism
-  Using the same module, we could export the assignment logic of every application in WEM
-  The user base has always been handheld with start menu shortcuts, 80% of the WEM configuration dealt with putting icons into the user start menu. As such:
    -  We moved the definition of these shortcuts into a Computer GPO, delivering the same shortcut into the shared start menu in the computer context. We used the same WEM assignment group to allow read permission to the shortcut to control the presentation. Note, when using this approach, I had to make sure that we configured the "Apply once and do not reapply" setting on the preference, else, the explorer process would flatline the CPU. It was important to structure GPOs appropriately to allow a cutover methodology over a range of change windows. This was painful work, but worth it
    -  We moved any Citrix Published Application shortcuts away from WEM, moved back to native workspace app integration and configured GPO to publish the applications into the same location as the WEM defined shortcuts
    -  We identified duplicate file copy handling of RDP shortcuts from a central repository to a temporary location on the machine (GPO) and then WEM actions to copy these shortcuts into the user start menu. These were moved into a GPP shortcut in the computer context with the same group filtering applied as above
-  We identified duplicate GPP assignments and simply removed them from WEM. Conversely, Reg key processing high CPU usage was a known issue in WEM, this was remediated in a recent release and implemented in the environment

The windows Start Menu is an index, so as long as the same shortcuts are presented in the appropriate locations, users have no idea (nor should they) and no change in functionality.

I built the end-state environment in the UAT environment, allowing me to throw users across both a production and test environment with the same profile. The results speak for themselves. A problematic user coming into the new environment would have a high WEM processing time as WEM undid its previous configuration (removed assigned shortcuts etc) to clean itself up, less time than normal, but still far too long. The second logon, however, would result in WEM processing taking **3-4 seconds** as opposed to in some scenarios, **4-5 minutes**. The CPU processing drop was immense, resolving both the WEM agent CPU time and the Cylance time in the same hit.

We implemented the changes over a set of 7 change windows. The average logon time is now sub 30 seconds, the majority sitting in the 23-26 second mark, I would like this to be lower however with GPP being the preference for drive mappings etc, there is not a lot that we can do there. We had one user who was consistently high with 8 minute logon times, down to 23 seconds, without noticing a single change to his user environment. The Nutanix platform is happy as Larry, with peak CPU usage now hitting 70 percent during the day vs 100% burn full time

## Summary and Learnings

What are the learnings here?

-  A consultative approach to troubleshooting is likely a better one than a vendor trying to sell more kit. The answer in front of the CIO was "buy competition HCI kit" - none of which would have helped the situation (this was not Nutanix pushing more kit FYI)
-  Application rationalisation is a must
-  WEM is a powerful beast, but it will process exactly what you ask it to - don't throw users with hundreds of group memberships at it, all with individual assigned applications and expect it to work magic
-  The term "user environment" does not mean everything has to be processed in the user context - use machine processing where you can to reduce user impact
-  Not every problem cause is obvious, no monitoring toolset was able to help us understand the intricacies of why processing was high, only that it was. Good old-fashioned log analysis was what identified what was going on here - thanks to the WEM product team who assisted with interpreting log file results
-  If you have to do silly things, deliver it in a fashion that does not impact the user experience
-  The logic of "why" is often lost. Using WEM to publish Citrix Applications was historical due to problems with 7.15 VDA years back, resolved but forgotten…so often legacy config hangs around without the need

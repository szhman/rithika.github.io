---
layout: post
title: "Getting To Know FSLogix Containers"
permalink: "/getting-to-know-fslogix-containers/"
description: "An introduction to FSLogix technology"
categories: [FSLogix]
redirect_from: 
    - /2018/02/20/getting-to-know-fslogix-containers
    - /2018/02/20/getting-to-know-fslogix-containers/
image:
  path: /assets/img/getting-to-know-fslogix-containers/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

FSLogix provides a fantastic set of features and capabilities across its relatively unique product set, particularly for those of us that live in a VDI or SBC world. I am going to have a quick look at the profile container and 365 container options and how they fit together to provide a pretty great experience without the overhead of traditional profile management and challenges associated with cache requirements in a cloud service-oriented deployment.

![Post Image](/assets/img/getting-to-know-fslogix-containers/fslogix1.png)

I recommend visiting Aaron Parker's and David Wilkinson's posts before deploying the solution into your environment

-  [deploying-fslogix-office-365-containers (Parker)](http://www.insentra.com.au/deploying-fslogix-office-365-containers/)
-  [fslogix-profile-containers-and-office-365-containers-deployment-guide (Parker)](http://www.insentra.com.au/fslogix-profile-containers-and-office-365-containers-deployment-guide/)
-  [fslogix-office-365-container-install-configure-and-testing (Wilkinson)](https://wilkyit.com/2017/05/10/fslogix-office-365-container-install-configure-and-testing/)

## Architectural Simplicity

FSLogix is built on simplicity, even its architecture is a simple one, comprising in the Profile and Office 365 Container spaces of three main components;

1.  A simple secured file share to host the Container disks (VHD, VHDX)
2.  A single agent installed onto your base image or template of choice
3.  Group Policy or registry settings to control the behaviour of the agent.

It really doesn’t get a lot simpler to drive than that, however there are of course considerations associated with each of the above control points.

## FSLogix Profile Containers

Profile Containers offer a full redirection of the existing profile into a container which is connected to the machine as soon as a user session is initiated. It is simply a redirection of the local profile to an alternate location, which happens to be a virtual disk or "container". It has brains and functionality associated with it which allow for specific annoyances to be exempt from the redirection, and also supports folder redirection for those that would still like to centralise their user data.

[What are profile containers?](https://www.fslogix.com/products/profile-containers)

Profile Containers are not a traditional profile management solution. Its beauty is in its simplicity. It's not managing the profile as such, its managing an alternate location for an existing profile to live, its effectively combining the speed of a local profile, with the benefits of roaming, and trying to negate the negatives associated with both.

Profile Containers can be configured via a few different tools:

1.  Group Policy ADMX files provided as part of the installation download
2.  Profile containers configuration tool: These settings are configured on your base VDA and allow for basic configuration of your Profile Containers
3.  Registry keys

A complete guide to the configuration settings can be found [here](https://docs.fslogix.com/display/20170529/FSLogix+Profiles+Configuration+Settings).

## FSLogix Office 365 Containers

Office 365 Containers are simply a subset of the capability provided by the FSLogix Profile Containers. It uses the same engine to drive, however it is aimed specifically to deal with the roaming of caches such as Microsoft Outlook, Skype for Business, Microsoft OneDrive and additionally, Windows search indexes and the Office 365 ProPlus activation.

[What are Office 365 containers?](https://www.fslogix.com/products/office-365-container)

Office 365 Containers is designed to support existing profile management solution – while a container-based approach to the profile introduces all sorts of benefits, some customers are likely happy with their existing profile management tool and are only looking to address the user experience with Office 365 applications.

Office 365 containers have progressed in their simplicity of management over the last 12 months, and should primarily be configured and managed by Group Policy, however registry key configurations are still valid and common.

A complete guide to the configuration settings can be found [here](https://docs.fslogix.com/display/20170529/Office+365+Configuration+Settings)

## The Power of the Combination

Whilst both the above solutions have their place in certain environments, the true power of this stack comes into play when you are using both in unison. The base tools to drive these solutions are the same, and they are smart enough to know how to deal with each other when both sets of functionalities are enabled and deployed together.

All of the FSLogix functionality is provided via a single agent. Even extending out to additional tools and offerings that FSLogix have in their suite of solutions, the core driving component is the same.

The basic process in the case of both technologies being at play is as follows:

1.  User Initiates a logon request to Windows
2.  FSLogix agent kicks in and mounts the Profile Container from the network location
3.  FSLogix agent rewrites the profile location at a kernel level to the Profile Container (This is the true beauty in its simplicity: Windows doesn’t think anything has changed)
4.  FSLogix agent then mounts the Office 365 Container
5.  FSLogix writes the location of specific data repositories stored in the %UserProfile%\ location to the Office 365 container in the same fashion as above
6.  FSLogix makes a decision on where to store the Search Database for the user. By default, if both technologies are in play, the search index will exist in the profile container, however depending on your deployment model this can be altered. See docs [here](https://docs.fslogix.com/display/20170529/Roaming+the+Windows+Search+Database)
7.  User Logon is completed by windows

This entire process takes a matter of seconds at most, typically less than this and governed by your storage and network capability, hence the importance of sizing and testing in any environment.

I have always tried to have a mindset that a user profile is a necessary evil that should be throw away. What I mean by this is that from a user perspective, a blown away profile should result in maybe a bit of inconvenience, but never any data loss. This is where folder redirection has always stepped in but has its own set of overheads.

With the move to Office 365 and the now huge requirements for local cache, the profile reset is now not only an inconvenience, but an outright pain not just for the user, but for the admin too - gone are the days where we can simply say "reset the profile" to address a specific issue or test a resolution. This is why combining Profile Containers and Office 365 Containers becomes a very smart move as we now have a lightning fast "local" profile container attached, but we also have all of our cache data for 365 safely sitting in a separate container.

There are still some considerations here to be wary of:

-  If you reset the user profile, you delete the outlook profile. When you do this, even though your OST file is still intact in your Office 365 Container, Outlook will not use it on a new profile build and will create a new OST. In Outlook 2013 onwards, you can choose to alter the OST in use and point it back to the existing OST to reuse the cache. [Change OST Location](https://support.microsoft.com/en-au/help/2752583/you-cannot-change-the-location-of-the-offline-outlook-data-file-ost-in)
-  OneDrive for Business is viewed as not configured and the cache is ignored upon a profile recreation. This could most probably be remediated via a registry export at logoff and import at logon process at the FSLogix layer - that would be nice to see
-  Search Indexes may be blown away and need a rebuilt - but that’s not a huge deal
-  Space. All of these things can chew up space. You don’t want to fill up a user's container if you can help it

I am keen to know of other challenges and how others address them, hopefully profile resets aren't being dealt with all that often given the nature of the solution at play

## Access Methods

Containers are a pretty simple concept once you draw it out, however there are some not so simple scenarios that we need to consider in the EUC world, concepts such as multi session RDSH or SBC environments, multiple VDI sessions, combine VDI and physical PC access requirements etc. David Wilkinson has a great write up on the different access models available with Office 365 Containers [here](https://wilkyit.com/2017/07/17/fslogix-concurrent-access-to-o365-containers-vhdaccessmode-explained/).

There is also some documentation from FSLogix on how to configured concurrent access with Profile Containers [FSLogix concurrent access](https://docs.fslogix.com/display/20170529/Concurrent+User+Profile+Access)

## Logging

This whole process sounds stupidly easy, and of course on paper it is (and in reality, it actually is) however you are going to no doubt run into things that you need to troubleshoot along the way. All actions are logged in a verbose fashion and there is utility to help troubleshoot and provide a status on disks and health: [FRXTray tool](https://docs.fslogix.com/display/20170529/FrxTray+Tool).

You can view the entire process and what decisions have been made by the driver, and any issues that might arise throughout the process. This is particular handy for troubleshooting missing disks, or slower than expected logons. Often there are timeouts, or file locks on the VHDX that simply don’t allow FSLogix to do what it wants to do in a timely fashion.

A guide to configuring Logging can be found [here](https://docs.fslogix.com/display/20170529/Logging)

## Folder Redirection

One would rightfully ask where folder redirection sits in all of this, and the answer is "it depends". The vast majority of deployments I have worked in still want and require folder redirection be implemented for backup or multi environment access requirements (VDI, RDSH, typical fat client desktop access etc).

FSLogix can existing perfectly happily alongside folder redirection with no extra configuration required, alternatively, it can simply house the data within the container for increased performance, albeit at the expense of multi environment access (more fit for VDI or RDSH only access).

An outside the square option (courtesy of [Aaron Parker](https://twitter.com/stealthpuppy)) is to redirect these folders into OneDrive for Business, allowing for sync back to Office 365 rather than a traditional File Share redirection. This would grant the benefits of increased performance around logons and file access, as the cache for this data would exist within the FSLogix Container itself, local to the user. Obviously, the existing considerations of OneDrive for Business and user data need to be taken into consideration.

Specific folders can also be exempted from Profile Containers and redirected to a local location as required

## Considerations

-  Antivirus: As per usual, antivirus can wreak havoc on profile containers if not dealt with correctly. Any time I see load time issues or locks on files, I go straight to Antivirus being the most likely cause
-  IOPS: IO in any profile solution is critical. If you have rubbish IO capability, your environment suffers, and this applies directly to profile containers. There Is not a lot of information on sizing for containers at the moment, but there is a great read from [Lee Jeffries](http://www.leeejeffries.com/my-experiences-sizing-fslogix-profile-and-o365-containers/) on his findings.
-  File system: If using Windows Server 2016 to host containers, use REFS file system, particularly if using differencing disks as the merge operations are almost instantaneous
-  Keep ADMX files up to date. With the release of 2.8.10, there is a huge amount of capability in the ADMX files that didn't exist before, and its much nicer to be able to view your configurations in one place
-  Container Sizing and Resizing. This is an interesting one, FSLogix supply a command line copy-profile option which will allow you to move an existing profile to a [new VHDX file](https://docs.fslogix.com/pages/viewpage.action?pageId=6554051) (Which you can pre stage as a bigger container)  however there is also the option of using diskpart to resize an existing container as outlined [here](https://support.fslogix.com/index.php/forum-main/profiles/139-profile-container-resize). The other more unique option available to you is to use Hyper-V Manager to inspect and expand the container size, mount it on a windows box and expand that disk space (OK [Arjan](https://twitter.com/menschab), credit where its due - it was his idea not mine )

## Additional Tools

-  Folder Redirection and Profile Migration: Profiles (Citrix and Microsoft) can also be migrated into a container using the [migration tool](https://docs.fslogix.com/display/20170529/Profile+Migration+Guide)
-  Exemptions: As alluded to above, you can [exempt specific content](https://docs.fslogix.com/display/20170529/Controlling+the+Content+of+the+Profile+Container) from the containers, and these requirements should be reviewed on a case by case basis

## Summary

I have touched only at a high level at what FSLogix Containers can do, and some of the considerations around deployment. There is plenty of good community reading available out there to get you up and running, and the reality is the solution is built on simplicity. It’s a simple concept, a simple deployment and every part of it has been designed to be simply managed.

There are rumblings around other technologies that seek to compete in this space, and maybe over time they will, but at this point in time FSLogix is way ahead of the game and offers features that make it a no brainer in this space. I am excited to see what the future holds, as what they have achieved to date is exciting

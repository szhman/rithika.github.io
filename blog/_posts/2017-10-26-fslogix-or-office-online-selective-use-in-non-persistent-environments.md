---
layout: post
title: "FSLogix or Office Online – Selective use in Non Persistent Environments"
permalink: "/fslogix-or-office-online-selective-use-in-non-persistent-environments/"
description: "Creating a consistent user experience for Office Online and Office Apps"
categories: [Citrix, FSLogix, Office 365, WEM, Windows]
redirect_from: 
    - /2017/10/26/fslogix-or-office-online-selective-use-in-non-persistent-environments
    - /2017/10/26/fslogix-or-office-online-selective-use-in-non-persistent-environments/
image:
  path: /assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

A common question that comes up when discussing FSLogix Profile Containers for Office 365 is "Do I have to licence all users that access my Citrix Environment"?

The answer is simply no. FSLogix Profile Containers for Office 365 is per user based however, often there are use cases in Citrix Environments where a high number of temporary or short-term staff are in play, and licencing these users for the full outlook experience in office 365 with FSLogix may not make sense. Outlook online is a perfectly viable and valid solution for these workers.

So how do we cater for environments where some need profile containers, and some don't? Three easy to drive technologies help here

-  AppLocker
-  Restricted Groups
-  Citrix Workspace Environment Management

Create an Active Directory Group to govern the solution `NoFSLogix_OfficeOnline`

[![GroupCreation]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/GroupCreation.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/GroupCreation.png)

## AppLocker

Restrict Access to the Office Suite via Microsoft AppLocker Policies. As an example, restrict the office executables from launching for members of the `NoFSLogix_OfficeOnline` Group. You will need to decide here on whether you want to block specific apps, or the entire office suite for users and provide them with their web based counterpart. Conversely, AppLocker policies can soon be driven by Citrix Workspace Environment Management which will tie in to part of this solution below AppLocker via GPO:

[![AppLocker]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/AppLocker.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/AppLocker.png)

AppLocker via WEM Application Security (still in preview):

[![AppLockerWEM]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/AppLockerWEM.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/AppLockerWEM.png)

**Note:** If you are dealing with high rotation users, there is a good chance you are not licencing them with a 365 Sku that contains office, and if you are using ProPlus with shared activation, then restricting access to any office executable that can't activate against the user is probably wise anyway.

If you are using KMS based licencing with non ProPlus Office and have a requirement for other components of the office suite to be used, then you can be more selective with your AppLocker Rules. At a minimum, I would suggest killing access to Outlook and OneDrive for Business (use their web counterparts). Remember that FSLogix Profile Containers can cater for OneDrive Roaming, Skype for Business OAB as well as Outlook OST files.

## Restricted Groups

On Installation, FSLogix will create multiple local groups on each server or desktop. Add the `NoFSLogix_OfficeOnline` Group to the local `FSLogix ODFC Exclude List` via Restrictive Groups (Group Policy). This will ensure that the FSLogix Driver does not attempt to create and attach a disk, and thus does not consume a licence for members of this group FSLogix Default Control Groups:

[![LocalGroups]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/LocalGroups.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/LocalGroups.png)

Restricted Groups via GPO:

[![RestrictedGroups]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/RestrictedGroups.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/RestrictedGroups.png)

Resultant Group Membership per host:

[![RestrictedGroups3]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/RestrictedGroups3.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/RestrictedGroups3.png)

## Citrix Workspace Environment Management

For Published Desktops or VDI environments, utilising Citrix Workspace Environment Management (Or GPP with item level targeting, but hopefully that’s in the past now) we can customise what applications are available and assigned to users, and how shortcuts are presented. See my post around managing start menus and shortcuts with WEM [here](https://jkindon.wordpress.com/2017/10/13/citrix-wem-modern-start-menus-and-tiles/).

With a little bit of logic, we can replace the traditional Outlook and Office based shortcuts, with their web based counterparts providing a relatively consistent user experience.

A simple example scenario is outlined below:

Members of `Domain Users` receive Office shortcuts as long as they are not a member of the `NoFSLogix_OfficeOnline` Group (I am using `Domain Users` for assignments with a WEM condition of `No Active Directory Group Match` for `NoFSLogix_OfficeOnline`)

[![WEMAssignment1]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/WEMAssignment1.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/WEMAssignment1.png)

WEM Condition:

[![WEMAssignment2]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/WEMAssignment2.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/WEMAssignment2.png)

Office Online shortcuts are assigned to the `NoFSLogix_OfficeOnline` Group

[![WEMAssignment3]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/WEMAssignment3.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/WEMAssignment3.png)

| **App** | **Web Equivalent** | **URL** |
| :-- | :--| :-- |
| Outlook | Webmail | [https://outlook.office.com/owa](https://outlook.office.com/owa) |
| Word | Word Online | [https://office.live.com/start/Word.aspx](https://office.live.com/start/Word.aspx) |
| Excel | Excel Online | [https://office.live.com/start/Excel.aspx](https://office.live.com/start/Excel.aspx) |
| OneDrive | ODfB Online | [https://tenantid.sharepoint.com](https://tenantid.sharepoint.com) |
| PowerPoint | PowerPoint Online | [https://office.live.com/start/PowerPoint.aspx](https://office.live.com/start/PowerPoint.aspx) |

The above URLS will be included in the next update for the [WEM Hydration Kit](https://jkindon.wordpress.com/2017/10/07/wem-hydration-kit/).

Sticking with my view on Start Menu management, I choose to create a folder in the start menu for my apps within WEM to align with everything else

[![WEMStartMenu]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/WEMStartMenu.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/WEMStartMenu.png)

Let's take a look at what this looks like below. Left hand side is my user who is restricted from using FSLogix, and I want him to use office online apps. Right hand side is my user who is more than welcome to use Office Apps, and can be happily supported in doing so via FSLogix Profile Containers

[![FSLogixSessions1]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/FSLogixSessions1.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/FSLogixSessions1.png)

As we can see, only my allowed user has a profile container

[![FSLogixSessions2]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/FSLogixSessions2.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/FSLogixSessions2.png)

Now if you haven't killed off things like the run command, and your restricted user tries to bypass the nice shortcuts you have provided him…Bam. Thanks AppLocker

[![AppLockerKill]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/AppLockerKill.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/AppLockerKill.png)

And really, it's not that ugly a situation for that user anyway...

[![FinalDesktop]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/FinalDesktop.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/FinalDesktop.png)

## Published Applications

For Published Applications, it's just a matter of publishing the Office 365 App URL rather than the executable for these users:

[![PublishedApps]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/PublishedApps.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/PublishedApps.png)

Storefront:

[![Storefront]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/Storefront.png)]({{site.baseurl}}/assets/img/fslogix-or-office-online-selective-use-in-non-persistent-environments/Storefront.png)

## Considerations

Native File Type Associations will prove a challenge. If you are using SharePoint Online as document repository then all is well, if you are still using file shares for document sharing, then this may not be the right fit for you

For Apps that specifically require access to office, then this model won’t work. Understand your target users and what they require.

There may be situations where you have your hand forced and users simply require the office suite. You may also be in a Hybrid scenario where you only need FSLogix for your 365 based users. In this case simply targeting who can and cannot use FSLogix may suffice, along with appropriate group policies to push on premises users into Online Mode (The happy days of Citrix and Exchange). The Solution is completely flexible even though the focus here is mainly for a full 365 user base

Activation. Get your activation experience right. ADFS or SSO, you want this to be smooth.

FSLogix Search Roaming is one of the coolest feature available from a happy user perspective. The challenge here is that enabling windows search is done a per machine basis, and as such represents a bigger challenge. My current recommendation here is that if you are utilising Windows Search roaming as part of your deployment, then deploy a second image and silo your non search users off accordingly. The last thing you want is windows search indexing spiking CPU and IO on your optimised and FSLogix'd Image for your production user base due to high turnover staff with non-roaming search indexes being built at each logon

A shout out to [Aaron Parker](https://twitter.com/stealthpuppy) over at Insentra who has been forever patient with my FSLogix questions over the last couple of years

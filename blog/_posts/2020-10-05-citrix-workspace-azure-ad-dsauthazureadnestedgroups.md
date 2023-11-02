---
layout: post
title: "Citrix Workspace, Azure AD & DSAuthAzureAdNestedGroups"
permalink: "/citrix-workspace-azure-ad-dsauthazureadnestedgroups/"
description: Azure Active Directory Groups not playing nicely with Citrix Workspace...
categories: [Apps and Desktops, Azure, Citrix, Cloud, Identity, Workspace]
redirect_from: 
    - /2020/10/05/citrix-workspace-azure-ad-dsauthazureadnestedgroups
    - /2020/10/05/citrix-workspace-azure-ad-dsauthazureadnestedgroups/
image:
  path: /assets/img/citrix-workspace-azure-ad-dsauthazureadnestedgroups/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Citrix Cloud and Azure Active Directory is a logical combination for many customers. The integration makes sense to provide a high level of security and access controls via the Microsoft Azure AD Conditional Access engine.

There have been instances, where integration with Azure Active Directory has not always consistently behaved as one might expect, in this post specifically, we are looking at a situation where users are intermittently (or in dire cases, never) displayed resources that they are entitled to by either direct assignment in Citrix Studio or via subscriber access in the library. If the environment is switched back to use native Active Directory, the problem does not exist.

Cheers to [Rob Sheppard](https://twitter.com/RobSheppard) for sticking with this and persisting over the months to get this sorted.

## Root Issue

The root problem is due to the way that SID based Groups are enumerated by Citrix Cloud for Azure Active Directory. A SID based group is defined as one that is created in Active Directory, not Azure Active Directory.

In a scenario where nested group memberships are in play, and the SID based groups are synchronized to AAD, the enumeration should work perfectly fine, however in the scenario where not all groups are synced, the DSAuth service can obtain group details through the Citrix Cloud Connector. The default mechanism for this lookup is an LDAP query for the groups defined in the **_TokenGroupsGlobalAndUniversal_** attribute of the user object. The challenge with this behaviour is that it does not expand nested Groups, and in some scenarios or Domain configurations, can lead to missing Group info and thus enumeration problems.

Azure Active Directory was the first "non Active Directory" IDP that was introduced into Citrix Cloud and as such identified what works, and what didn’t. lessons learned were included for additional IDP’s. Enter **_DSAuthAzureAdNestedGroups_**.

**_DSAuthAzureAdNestedGroups_** fundamentally changes how the Group enumeration occurs. When enabled the feature effectively performs a Kerberos S4U login, in which Windows does almost the same process as a normal logon, where all the nested groups are retrieved and a more accurate determination of the group membership is made.

**_DSAuthAzureAdNestedGroups_** is the default behaviour for all other IDP's onboarded onto the Citrix Cloud solution, however with Azure Active Directory, it was felt to be safer by Citrix to not fundamentally change what wasn’t broken for everyone, and as such the feature is a toggle that needs to be applied on a per-customer basis.

## How to Resolve

If you are experiencing problems with Azure AD group enumeration, you will need to lodge a ticket with Citrix support and reference the **_DSAuthAzureAdNestedGroups_** toggle for escalation purposes. Note that doing so, may well alter existing group assignments in place, so it’s worth planning, scheduling and testing this change accordingly to make sure that nothing unexpected occurs

Some basic good practices for Azure Active Directory integration with Citrix Cloud:

-  Sync as many groups as you can and try to avoid nested Group scenarios where not all Groups are synchronized
-  Make sure that your Active Directory is well connected with your Cloud Connectors. Cloud Connectors are sensitive to Active Directory latency and any form of communication failure can have random effects and behaviours on operations

Hopefully, this will assist when troubleshooting enumeration issues when using Azure Active Directory as your IdP of choice. As always, thank you to Oscar Day at Citrix for taking the time to review and assist in getting this communicated

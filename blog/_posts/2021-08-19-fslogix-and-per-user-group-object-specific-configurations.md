---
layout: post
title: "FSLogix and Per User/Group Object Specific Configurations"
permalink: "/fslogix-and-per-user-group-object-specific-configurations/"
categories: [FSLogix, CVAD, Windows, Profiles, Policy]
redirect_from: 
    - /2021/08/19/fslogix-and-per-user-group-object-specific-configurations
    - /2021/08/19/fslogix-and-per-user-group-object-specific-configurations/
description: Getting flexible with FSLogix configurations
image:
  path: /assets/img/fslogix-and-per-user-group-object-specific-configurations/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Firstly, I simply could not find an image that represented what I was talking about here. So I stuck a picture of a container at the top…. I amuse myself. Anyhoo…

FSLogix has been heralded for it's simplicity in profile management and administrative "lack of" required configuration, Turn it on, walk away, and for the most part, you are done.

This of course is half the story, high availability, performance impacts, and capacity management etc. of course become a significant set of considerations for customers of all sizes.

But I digress. The focus of this post is to run through some of the more advanced thinking that the FSLogix developers had in mind, when they developed what appears to be a very simplistic solution. Primarily this comes in the form of `ObjectSpecific` configurations, or for the more human amongst us, per user or per group configurations.

FSLogix writes its configuration in the machine context, often misleading people into thinking its 100% machine based configurations with no flexibility, This is not entirely accurate. `ObjectSpecific` keys allow us to write user or group based configurations in the machine context, providing us with differing behaviours for different user groups that log into the same server. This is often missed and often addresses all sorts of random use cases that would, in a machine only configuration, be quite painful. You can [read up on the feature on Microsoft Docs](https://docs.microsoft.com/en-us/fslogix/configure-per-user-per-group-ht)

I wrote briefly about this a while ago in my architecting for [active-active profile management post](https://jkindon.com/2019/10/15/designing-profile-management-with-active-active-resource-locations/), but it warrants a quick dedicated post.

By default FSLogix writes it’s configuration to the following locations:

-  `HKLM\Software\FSLogix\Profiles (for Profile Container)`
-  `HKLM\Software\Policies\FSLogix\ODFC (for Office Container)`

User or Group based configurations simply need to be appended to these paths by creating a new key in the following format:

-  `HKLM\Software\FSLogix\Profiles\ObjectSpecific\{SID} (for Profile Container)`
-  `HKLM\Software\Policies\FSLogix\ODFC\ObjectSpecific\{SID} (for Office Container)`

Under each of those keys, you can then enter the appropriate configuration you need to override

-  [For Profile Containers](https://docs.microsoft.com/en-us/fslogix/profile-container-configuration-reference)
-  [For Office Containers](https://docs.microsoft.com/en-us/fslogix/office-container-configuration-reference)

The easiest way to find the required SID is using either Get-ADUser or Get-ADGroup PowerShell commands:

```
Get-ADUser RandomUser | Select-Object Name,SID

Get-ADGroup RandomGroup | Select-Object Name,SID
```

## Use Cases

Below are some use cases for `ObjectSpecific` FSLogix Configurations

-  You are undergoing a migration to a new file storage repository and want to test or stage users accessing the new location. This is good for large deployments that don’t want a big bang cutover
-  You want to distribute specific user groups to different storage locations
-  You have different profile type requirements for different users sets. Eg, you may need some people to use a `Type 3` profile, but you don't want to punish the entire user base with differencing disks, when a `Type 0` is perfectly fine
-  You need to test changes to the environment and don’t want the hassle of deploying UAT servers and directing users to that UAT environment
-  You want to play with Cloud Cache for some users, but not others (careful)

## Summary

That’s about it for this post. Purely a quick post to shed some light on what appears to be a blind spot for FSLogix deployments. Has helped me out in multiple deployments

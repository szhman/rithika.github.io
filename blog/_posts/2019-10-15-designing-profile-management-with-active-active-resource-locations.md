---
layout: post
title: "Designing Profile Management with Active-Active Resource Locations"
permalink: "/designing-profile-management-with-active-active-resource-locations/"
description: "Profile considerations and options for multi site active-active deployments"
categories: [Apps and Desktops, Citrix, FSLogix, Profiles, UPM, Windows]
redirect_from: 
    - /2019/10/15/designing-profile-management-with-active-active-resource-locations
    - /2019/10/15/designing-profile-management-with-active-active-resource-locations/
image:
  path: /assets/img/designing-profile-management-with-active-active-resource-locations/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

For a while now my friend [Brandon Mitchell](https://twitter.com/BMitchell76) and I have been throwing ideas back and forward around how we see things unfold with profile management across multiple resource locations, both from a Citrix UPM and FSLogix perspective. We both have different insights based on our respective roles; I am a consultant who sees many different environments, Brandon operates within a monster environment that has to manage scale considerations. As such, we decided to put a series of blog posts together to show some insight into how we cater for different scenarios. This is part 1 of 3 posts where we want to tackle the following:

1.  Citrix UPM and FSLogix across multiple active resource locations (on-prem or cloud)
2.  Citrix UPM and FSLogix considerations at scale
3.  Is profile management the best solution?

## Designing Profile Management Across Active-Active Resource Locations

The idea of a truly active-active datacenter is a nice one, however full of challenges and considerations.

In the Citrix world, we live and die by the simple rule of `keep your users as close to their data and applications as possible`. Whilst this is not always achievable, deviations from this path must be clearly identified and the impacts of doing so understood and communicated.

In this post, I will focus on a small component of this extremely large conversation, and run through some logic on how to handle profile management, both Citrix UPM and FSLogix within environments that consume one or many active resource locations.

I am purposely not going into Cloud Cache technologies in this scenario in relation to FSLogix and focusing purely on VHD Locations.

## The Challenge

Many resource locations are configured with high quality interconnects effectively making multiple locations appear and act as one. We regularly see environments more than capable of handling user-based compute _<u>load</u>_ in multiple resource locations, with links quality enough to house profile data in a single chosen location, however, we also see many that are not, or need for whatever reason (business, political, or technical) to be active-active whilst also somewhat independent.

The underlying challenge is that whilst we are all familiar with replication technologies, distributed file system namespaces (DFS-N) and the ability to localise access to replicated datasets, we are constantly governed by the overarching fact that neither user data (folder redirection) or user profile data is supported by either [Microsoft](https://blogs.technet.microsoft.com/askds/2010/09/01/microsofts-support-statement-around-replicated-user-profile-data/) or [Citrix](https://docs.citrix.com/en-us/profile-management/current-release/plan/high-availability-disaster-recovery-scenario-2.html) in a two way replication scenario.

Simply put, you cannot have a user access resources in Sydney and access their profile data housed in Sydney, whilst also allowing the same user to launch a new session in Melbourne and access a replica of that _<u>same</u>_ data housed locally to Melbourne hosting servers, in a configuration where that data is allowed to replicate *back* to Sydney.

This is effectively multi-master two-way replication and is provided in many organisations by technologies such as DFS-R. I am not saying it does not work and I know organisations that do this, however, it is not supported and the risks of data corruption is high.

Below is an architecture that has worked to address these challenges somewhat across multiple engagements successfully, it's not rocket science yet it has been consistently successful to date.

We will define this as Active-Active resource locations with user home assignments – or more simply: home zone with failover.

The following defines this scenario for an example of Sydney and Melbourne resource locations:

-  Both Sydney and Melbourne resource locations can actively handle _<u>user load</u>_ in an active-active fashion
-  Site Aggregation or Zone Assignment is used to assign users to the correct resource location in a preferred manner
-  An Active Directory attribute or supported environment variable is used to segment profile data for the user sets (for Citrix UPM only)
-  Each user set has their primary profile store in their home resource location (either Sydney or Melbourne)
-  Each server hosting user load in each site points to the appropriate site-local file storage location
-  Should a user failover to their non-preferred resource location, they will access a lagged replica of their profile. Changes will <u>not</u> be replicated back to the primary location in scenarios where users access their profile data in their non-preferred resource location

Both examples below will work based on the following assumptions

-  There are two active resource locations, Sydney and Melbourne
-  Users in Sydney will access resources in the Sydney based resource location
-  Should the Sydney resource location be offline, Sydney users can access their resources via the Melbourne based resource location
-  Users in Melbourne will access resources in the Melbourne based resource location
-  Should the Melbourne resource location be offline, Melbourne users can access their resources via the Sydney based resource location

## Citrix Profile Management

Citrix Profile Management operates in the above model with the following requirements and configurations:

-  There must be an identifier available to segment the user base. This may be any available Active Directory attribute or supported environment variable. In this scenario I am going to be assuming the AD Attribute `location` for the user base is going to be set to either `Sydney` or `Melbourne`
-  The actual file system path in each resource location will utilise a folder that matches the chosen environment variable for the UPM path resulting in the following:

| **Resource Location** | **Profile Path** | **#l# expanded** |
| :-- | :-- | :-- |
| Sydney | `\\SYD-FS1\Profiles\#l#\%UserName%.%UserDomain%` | `Sydney` |
| Melbourne | `\\MEL-FS1\Profiles\#l#\%UserName%.%UserDomain%` | `Melbourne` |
{:.smaller}

With the above profile paths configured, the following scenarios occur for James, who is a `Sydney` user:

| **Resource Location** | **Profile Path** | **Data Status** |
| :-- | :-- | :-- |
| Sydney (Preferred) | `\\SYD-FS1\Profiles\Sydney\%UserName%.%UserDomain%` | Primary |
| Melbourne (Secondary) | `\\MEL-FS1\Profiles\Sydney\%UserName%.%UserDomain%` | Replica |
{:.smaller}

With the above profile paths configured, the following scenarios occur for Brandon, who is a `Melbourne` user:

| **Resource Location** | **Profile Path** | **Data Status** |
| :-- | :-- | :-- |
| Melbourne (Preferred) | `\\MEL-FS1\Profiles\Sydney\%UserName%.%UserDomain%` | Primary |
| Sydney (Secondary) | `\\SYD-FS1\Profiles\Sydney\%UserName%.%UserDomain%` | Replica |
{:.smaller}

The below image outlines Citrix UPM in a lagged replica multi-site deployment

[![Citrix UPM in a lagged replica multi-site deployment]({{site.baseurl}}/assets/img/designing-profile-management-with-active-active-resource-locations/UPM.jpg)]({{site.baseurl}}/assets/img/designing-profile-management-with-active-active-resource-locations/UPM.jpg)

## FSLogix Profile Container

FSLogix Profile Container does not offer the same path flexibility that Citrix UPM does. It cannot currently look at an Active Directory Attribute and adapt the path accordingly. It does offer the ability to override a defined VHD path based on Active Directory Group membership, which opens the door to achieve the same model of segregation as UPM above. The following configurations and requirements apply to FSLogix Profile Container:

-  `Sydney` and `Melbourne` users must live in a unique Active Directory group. This group is used to identify their preferred resource location. If you are utilising StoreFront based Site Aggregation or Zone assignment you would have this available already
-  Group membership-based settings can [override machine-based settings](https://docs.microsoft.com/en-us/fslogix/configure-per-user-per-group-ht) for profile locations
-  The actual file system path in each resource location will utilise a folder that matches the differentiator resulting in the following:

`Sydney` users will have the following paths configured depending on their resource location

| **Resource Location** | **Profile Path** | **Data Status** |
| :-- | :-- | :-- |
| Sydney | `\\SYD-FS1\Containers\Sydney` | Primary |
| Melbourne | `\\MEL-FS1\Containers\Sydney` | Replica |
{:.smaller}

`Melbourne` users will have the following paths configured depending on their resource location

| **Resource Location** | **Profile Path** | **Data Status** |
| :-- | :-- | :-- |
| Melbourne | `\\MEL-FS1\Containers\Melbourne` | Primary |
| Sydney | `\\SYD-FS1\Containers\Melbourne` | Replica |
{:.smaller}

<br>
- Each path defined will be inverted per resource location
- Use Multiple VHD locations per user group if failover across the resource locations is required in the event of a lost file server. For example, the `Sydney` site is still actively accepting user load, however, the `Sydney` File Servers are dead. We may want to failover to `Melbourne` File Servers.

The below would be configured for users within each resource location

| **Resource Location** | **Profile Path** | **Data Status** |
| :-- | :-- | :-- |
| Sydney | `\\SYD-FS1\Containers\Sydney` <br> `\\MEL-FS1\Containers\Sydney` | Primary <br> Replica |
| Melbourne | `\\MEL-FS1\Containers\Melbourne` <br> `\\SYD-FS1\Containers\Melbourne` | Primary <br> Replica |
{:.smaller}

The below image outlines FSLogix Containers in a lagged replica multi-site deployment

[![FSLogix Containers in a lagged replica multi-site deployment]({{site.baseurl}}/assets/img/designing-profile-management-with-active-active-resource-locations/FSLogix.jpg)]({{site.baseurl}}/assets/img/designing-profile-management-with-active-active-resource-locations/FSLogix.jpg)

An example of the configuration outlined above is displayed below:

[![Example GPO for Group Override with FSLogix]({{site.baseurl}}/assets/img/designing-profile-management-with-active-active-resource-locations/ExampleConf.png)]({{site.baseurl}}/assets/img/designing-profile-management-with-active-active-resource-locations/ExampleConf.png)

## Considerations

-  This model requires that you consistently and regularly replicate data from the users' primary resource location to their secondary. Tools such as robocopy are the low hanging fruit here, or there is a myriad of options available in the windows space (I am not going into a storage replica style discussion here).
-  If you want guaranteed replication based on block-level replication in the windows space, look into [bvckup2](https://bvckup2.com/kb/beyond-robocopy) it will handle both technologies gracefully and reliably within the windows file server world, I wrote about this [previously](https://jkindon.com/2019/08/26/architecting-for-fslogix-containers-high-availability/)
-  User data must be handled in a similar fashion, if you are using folder redirection, alternate paths and replication must once again occur, however, the same rules apply for support in multi-master scenarios
-  User data should be architected in a way that ideally removes any pure dependency on the user profile being two way replicated. By this, I mean that if there is critical data in the user profile that only lives in the profile, and changes in a failover scenario relating to that data are critical, then that data simply lives in the wrong place (I will write a follow-up post around this). A profile should be expendable
-  The examples shown are very simple, you may choose to consume DFS-N in your path configurations to simplify the number of policies required for paths

## Summary

This article discusses some basic concepts associated with profile management in a high-level form, it does not delve into additional options and technologies such as FSLogix Cloud Cache to address multi-site requirements, and also deals only with profile management, there are many other considerations to take a look into when designing for active-active resource locations, each and every one of them worthy of an article by themselves. I hope this has helped in some small way, and am excited about the next post in the series which Brandon is currently working on.

Until next time….

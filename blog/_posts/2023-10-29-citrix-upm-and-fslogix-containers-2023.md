---
layout: post
title: Citrix UPM and FSLogix Containers in 2023
permalink: "/citrix-upm-and-fslogix-containers-2023/"
categories: [Citrix, UPM, FSLogix, Profiles, Windows, CVAD]
description: Delving into the when, why and why not of Citrix UPM and FSLogix Profile Containers in 2023
image:
  path: /assets/img/citrix-upm-and-fslogix-containers-2023/post_default_image.jpg
sitemap: true
hide_last_modified: false
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

A few years back, I put together [my thoughts on Citrix Profile Management (CPM) and FSLogix Profiles](https://jkindon.com/citrix-upm-and-fslogix-containers/) and where they make sense. That post is over two years old now, and the world moved on significantly. I have updated the old post with some commentary around the changes, but felt it was nice to keep this historical data.

This post is designed as a brief point in time thought provoker on where I see the same two technology sets and their current state of play.

Again, as per last time, this article is Citrix focused. If you don't have access to CPM technology, then as you were soldier.

## The Rise and Stagnation of FSLogix

FSLogix came to the table with a bang. Office Containers, Profile Containers and AppMasking. All capabilities that were ground-breaking and changed how we could deliver solutions in the EUC space. The Office Container was a huge enabler for customers moving to M365, whereas in reality, a Profile Container wasn't a new concept, FSLogix just did a fantastic job at executing on it, vs Microsoft’s own User Profile Disk in RDSH.

FSLogix was led by some very cool and massively switched on humans. The company was cool, the technology was great, the trajectory was huge, and the people were fun. Good times ahead. Until they were acquired by Microsoft.

At first things seemed great. FSLogix for everyone effectively. What could go wrong? Read the [historical rants](https://jkindon.com/citrix-upm-and-fslogix-containers/) I have on exactly why could, and did go wrong with this model. A default lean towards Containers for everything, outages everywhere due to the wrong solutions being implemented in the wrong context, laziness aplenty and a lack of understanding on what was actually being introduced and more importantly, [why it was being introduced](https://jkindon.com/profile-management-in-2019-what-how-why/).

At the same time as this was going, Citrix Profile Management was somewhat forgotten about and pushed to the side for many organisations and consultants. A sad reality given its history, but also somewhat understandable given how much you *did_not_have_to_do* with FSLogix Containers. Luckily for us, Citrix never appeared to be worried, and kept building, building, building.

`The age of file based profiles was dead`. Was it? `Citrix CPM is dead, everyone is going to FSLogix`. Hrm was it? `FSLogix is free`. Within reason sure. But there was always a cost somewhere else.

Without this being a history lesson, two key things happened, or more accurately, continued to happen over the last few years.

1.  Microsoft did as aptly put by my friend Oz, purchased FSLogix, and "After they acquired it, it was told to sit in a corner and shut up". Sensational. It's not like the product got bad; it just didn't progress. Support was/is horrific, bugs aplenty (but to be fair, it's software and there are bugs everywhere, the challenge is the time to dignify and fix it), and it appears as if Microsoft’s own support had no real concept of what this "FXLogix" thing was. That is not a typo, that is the common wording used to describe FSLogix by MS support.
2.  Citrix continued to build CPM. They obviously took a lot of the learnings and ideas from FSLogix, but they built, they supported, they innovated, and they executed continually on new features. Throw them an idea, they built it. File based, Container based, selective use of either, resilience, replication, compaction, GPO processing tricks, the list goes on.

For where we stand now, I struggle to find a reason for any Citrix customer to not use CPM capability, be it Container or Files, in their deployments. Outside of the effort to change, I don't think (and am happy to be wrong) that FSLogix Containers offer anything that Citrix technology does not.

It is absolutely no surprise, that we have reached a pivot point where the conversations are now back to a CPM lead. Just look at the progression of the solution on a [quarterly release cadence](https://jkindon.com/the-evolution-of-citrix-profile-management/).

## Mapping the Container Capability between CPM and FSLogix

Whilst Citrix have done a great job at building their capability, they have not done a great job of advertising what they have built. Below is a quick high-level mapping of the cool stuff between technologies where I think it's relevant:

| Capability | FSLogix | CPM |
| :--- | :--- | :--- |
| Profile Disk Modes | [Direct, Try for read write, fallback to RO, RW and RO](https://learn.microsoft.com/en-us/fslogix/reference-configuration-settings?tabs=profiles#profiletype) [Profile Modes](https://learn.microsoft.com/en-us/fslogix/reference-configuration-settings?tabs=profiles#profiletype) | RW by default. [Excusive Access](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/policies/reference/profile-management/profile-container-policy-settings.html#enable-exclusive-access-to-vhd-containers) if enabled ✅ |
| Multi Location Resiliency | [Cloud Cache](https://learn.microsoft.com/en-us/fslogix/concepts-fslogix-cloud-cache) | [Replicate User Stores](https://docs.citrix.com/en-us/profile-management/current-release/configure/replicate-user-stores) and [Local Caching for Containers](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/policies/reference/profile-management/profile-container-policy-settings.html#enable-local-caching-for-profile-containers) ✅ |
| Office Data | [ODFC Container](https://learn.microsoft.com/en-us/fslogix/concepts-container-types#odfc-container) | [OneDrive Container](https://docs.citrix.com/en-us/profile-management/current-release/configure/enable-the-onedrive-container), [Outlook Search Experience](https://docs.citrix.com/en-us/profile-management/current-release/configure/enable-native-outlook-search-experience) ✅ |
| UWP | [InstallAppXPackages](https://learn.microsoft.com/en-us/fslogix/reference-configuration-settings?tabs=profiles#installappxpackages) | [UWP App Roaming](https://docs.citrix.com/en-us/profile-management/current-release/configure/uwp-app-roaming) ✅ |
| Space Reclamation | [VHD Compaction](https://learn.microsoft.com/en-us/fslogix/reference-configuration-settings?tabs=profiles#vhdcompactdisk) | [Compaction](https://docs.citrix.com/en-us/profile-management/current-release/configure/vhd-disk-compaction#overview) ✅ |
| Multi Access | Mode 3 Profile Disks with limitations ❌ | [Multi Session write-back](https://docs.citrix.com/en-us/profile-management/current-release/configure/enable-multi-session-write-back-for-profile-containers) ✅ |
| Selective Containerization | Nil ❌ | [Large File Handling](https://docs.citrix.com/en-us/profile-management/current-release/configure/to-enable-large-file-handling) ✅ |
| Async GPO Processing | Reverted ❌ | [Async Processing](https://docs.citrix.com/en-us/profile-management/current-release/configure/enable-async-for-user-gpo) ✅ |
| Auto Expansion | [Dynamic Disks](https://learn.microsoft.com/en-us/fslogix/concepts-vhd-disk-compaction) | [VHD auto-expansion](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/policies/reference/profile-management/profile-container-policy-settings.html#enable-vhd-auto-expansion-for-profile-container) ✅|
{:.smaller}

As you can see, by my math, there is nothing that FSLogix does, that CPM does not outside of specific Read Only profile disk scenarios.

Note that this is purely related to Container technologies. There is a whole lot more that they bring to the table when you look at file based as well.
{:.special_note}

## References and Documentation

A few handy references hidden away in the Citrix documentation

-  A handy list of policies and what applies to [container vs files](https://docs.citrix.com/en-us/profile-management/current-release/policies/policy-application-table)
-  A [policy version table](https://docs.citrix.com/en-us/profile-management/current-release/policies/settings) allowing you to see changes against each version of CPM
-  Guidance on [improving the logon experience](https://docs.citrix.com/en-us/profile-management/current-release/how-to/improve-user-logon-performance)

## Summary

This write-up is not about down talking FSLogix, but more about raising awareness around the current state of capabilities with CPM, and why it might make more sense. There are so many conversations that we regularly have where people have absolutely no awareness of how fast things are moving in the CPM world, and we still hear misinformation being spread based on concepts from years ago which are simply not true.

Are you going to be in trouble or have a bad experience if you use FSLogix? Absolutely not. It's a great tool. But if staying supported, having significant innovation and development along with ongoing enhancements is important to you, then CPM is going to look a lot more appealing.

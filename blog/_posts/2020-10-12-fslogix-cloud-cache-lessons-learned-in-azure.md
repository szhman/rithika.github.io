---
layout: post
title: "FSLogix Cloud Cache - Lessons learned in Azure"
permalink: "/fslogix-cloud-cache-lessons-learned-in-azure/"
description: Sharing some lessons learned on FSLogix Cloud Cache in Microsoft Azure
categories: [Apps and Desktops, Azure, Cloud, FSLogix, Profiles, Storage Accounts, Windows]
redirect_from: 
    - /2020/10/12/fslogix-cloud-cache-lessons-learned-in-azure
    - /2020/10/12/fslogix-cloud-cache-lessons-learned-in-azure/
image:
  path: /assets/img/fslogix-cloud-cache-lessons-learned-in-azure/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

The long-awaited dream of FSLogix Cloud Cache is now one that I am willing to suggest is a reality. The technology is amazing in what it has promised for so long, and to be fair, _functionally_ it has been pretty good for a while. It has been the performance tax, and inconsistency of that impact which has left me very much avoiding it in production for several years. Environments running high levels of compute and storage may not have experienced these older challenges, but those that have all sing the same tune of "ouch". As of the FSLogix Apps release 2004 (2.9.7349.30108), the technology is working beautifully, and the logon/logoff performance tax has been massively reduced.

I am putting together this post to capture lessons learned, and those lessons that will continue to be learnt with a focus on deploying FSLogix Cloud Cache in Microsoft Azure. These components will be based on my experiences and I welcome additional considerations or learnings from the community which can be added to the list.

## Design Considerations

-  Avoid IaaS technical debt. Where possible, move to PaaS offerings such as Azure Files or Azure NetApp Files when deploy FSLogix workloads (including Cloud Cache) into Microsoft Azure. Both offerings now offer Active Directory integration, encryption and scale. Some organizations are not always happy moving to PaaS offerings for [architectural or business reasons](https://jkindon.com/2020/05/27/navigating-azure-storage-options-for-fslogix-containers/), however IaaS offerings are not always bullet proof either. Watch the ~20 second delay on logons for Profile Containers housed on SOFS on S2D deployments in Azure. It is not documented, but it’s real and a fundamental problem which has only now been addressed by the ability to utilise [shared disk access](https://docs.microsoft.com/en-us/azure/virtual-machines/disks-shared-enable?tabs=azure-cli) to managed disks in Azure
-  If consuming Azure Files, [do not fly blind](https://docs.microsoft.com/en-us/azure/storage/common/storage-monitoring-diagnosing-troubleshooting?toc=/azure/storage/files/toc.json#monitoring-performance). If using Azure Files Premium, you should be very aware of the IOPS vs capacity scaling equation, and the [burst capability](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares). Do not operate in the burst bucket consistently. If you are, this is an indication that it is time to scale up. Use Azure Monitor to [alert on these events](https://docs.microsoft.com/en-us/azure/storage/files/storage-troubleshooting-files-performance#how-to-create-an-alert-if-a-file-share-is-throttled). When you are out of juice, your users hurt, and Cloud Cache will up your juice requirements
-  The curse of read-write disks. I have no idea on the "why" of this one, but when using RW disks with Azure Files (mode 1 or mode 3), we found there is massive tax on performance and IOPS. The IOPS figures on both the local machine and the Storage Account seem to go through the roof. This could be unique to the engagement I saw this on, but I am calling it a curse and if you do not need differencing disks, stick with Mode 0 – Direct Access VHD. The performance difference is wild
-  The majority of Azure VM sizes that are appropriate for EUC workloads will come with a temporary SSD which is flushed on each machine reallocation. This SSD is purely for temp data but offers a nice win for reducing the IOPS impact of Cloud Cache on your system disk (default location). There is Group Policy from FSLogix to control this location, however you need to bake the "Cache" location change in your master image prior to sealing (yes you can bake in via GPO). GPO does not apply fast enough for this change to occur at machine boot, so whilst the "WriteCache" will move, the "cache" will not. This is only a problem for non-persistent deployments. Documentation on this is [found here](https://docs.microsoft.com/en-us/fslogix/cloud-cache-configuration-reference#cloud-cache-disk-registration-settings-overview). I have lodged a feature request with the awesome [BIS-F team](https://github.com/EUCweb/BIS-F) for this one so that we can tackle it during sealing
-  Do not underestimate IO requirements. It is expected that Cloud Cache is going to require more IOPS. You are reading into local cache on the machine initially, then performing read and writes consistently through the session, and writing back consistently to multiple backend locations. This is taxing, but it’s the cost of living the true profile redundancy dream
-  Ensure that aggressive AV solutions are configured properly. Exclude the FSLogix WriteCache and Cache directories. If you move these locations, update your AV exclusions
-  If you are using Cloud Cache with multiple Storage Accounts for redundancy such as cross region failover, ensure that you configure your production and failover workloads with the appropriate location as the primary (hone primary workloads to primary storage accounts). Also be aware that your logoff delays are dependent on the slowest write. If you have a storage account in the UK, and your primary workloads are in Australia, then logoff delay will be directly related to how fast the VM can flush to the UK Storage Account
-  Understand the implications of Cloud Cache on your DR testing scenarios. Cloud Cache has smarts inbuilt to understand which storage repository is more up to date. If you invoke DR against your secondary CC location (think isolated DR testing), you may well impact your primary storage repository once connectivity is brought back online as the metadata in the secondary location is more recent than the primary
-  Division of Profile and Office Container is a constant discussion point. Some split, some do not, some split storage locations so that we can move specific containers to different storage performance tiers, manage backup or simply discard cache data easily. CC has the same considerations as VHD locations, and with Azure Files, it's important to think about capacity and IOPS limits and how they effect your Share provisioning (the limits are configured at a share level). I have a [script here](https://github.com/JamesKindon/Citrix/blob/master/FSLogix/DistributeContainerShares.ps1) which builds on the work of [Ryan Revord](https://twitter.com/rsrevord) outlined in [James Rankins post around placing users on the share with the most free space](https://james-rankin.com/articles/spreading-users-over-multiple-file-shares-with-fslogix-profile-containers/)

## Management

-  Logging is key to everything FSLogix. When dealing with Cloud Cache you will find several useful log files, however you will need to enable FSLogix logging for "All Logs Enabled" and not selective logging. If you don’t do this, you will have no Cloud Cache log files under c:\programdata\fslogix\logs and be simply stuck with the transactional log under c:\program files(x86)\fslogix\apps\
-  Everyone should consistently go back and read [Aaron Parkers article on Container Management](https://stealthpuppy.com/fslogix-containers-capacity/). And then read it again. And then again.
-  Citrix Workspace Environment Management Service has a [Profile Container Insights](https://docs.citrix.com/en-us/workspace-environment-management/service/user-interface-description/monitoring.html#profile-container-insights) feature currently being deployed. This requires the WEM service and version 2008 onwards of the WEM service agent. Worth a look

## Known Issues

-  Azure Files timeouts on first load. We experienced an issue with Azure Storage accounts where after an undefined period of time, the storage accounts would effectively "go quiet". This quiet period occurred when there was no active connections to the share, and would cause a huge delay on the poor human who first had to launch a session. Microsoft dignified, and to their credit appeared to fix the issue for us, with feedback outlining that the fix would be pushed to all customers. So hopefully you don't experience this, however if you do, mitigations such as scheduled session launches may assist ([Scout Bees](https://scoutbees.io/), [Desktop Probing](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/director/troubleshoot-deployments/applications/desktop-probing.html) etc)
-  Whilst attempting to test a scenario where a storage account was lost, a couple of behaviours were noted:
    -  Blocking access on the storage account firewall to source subnet was not sufficient to represent the loss of a storage account and the sessions would fail to load
    -  Removing the share entirely was not sufficient to represent the loss of a storage account and the sessions would fail to load
    -  Physically killing the storage account does the trick. Something in the way the storage accounts still respond hurts Cloud Cache

## Summary

To summarise, FSLogix Cloud Cache always promised the golden goose of profile availability, and finally it is working beautifully. Azure Files offers a perfect repository for storing Containers, reducing technical debt and providing immense scale. I had the pleasure of working with a great customer on a good scale deployment where many of these lessons were learned on the fly, so if you are reading this, kudos for being on the edge and pushing through the learnings.

I intend for this article to be updated regularly with findings and learnings and as always welcome feedback and input

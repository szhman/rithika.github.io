---
layout: post
title: "Navigating Azure Storage options for FSLogix Containers"
permalink: "/navigating-azure-storage-options-for-fslogix-containers/"
description: Diving into the options storing FSLogix Containers in Microsoft Azure
categories: [Azure, Citrix, Containers, FSLogix, Profiles, Windows]
redirect_from: 
    - /2020/05/27/navigating-azure-storage-options-for-fslogix-containers
    - /2020/05/27/navigating-azure-storage-options-for-fslogix-containers/
image:
  path: /assets/img/navigating-azure-storage-options-for-fslogix-containers/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

User hosting solutions in the Microsoft Azure Cloud platform are the latest in a long trend of workloads to now be at the forefront of conversations, specifically those that benefit from leveraging FSLogix Containers.

This post aims to address the current options available for hosting FSLogix Containers in Microsoft Azure, with some pros and cons associated with each option based on experiences to date.

There are a few key architectural considerations that will ultimately lead to the best fit solution for your environment, some examples outlined below:

**Security:** Security teams and requirements will often trump any other preference or direction. Understanding security concerns and drivers along with any specific requirements for your organization is key. Your options may well be limited from the word go and your path chosen for you by the security directives

**Performance:** With Container technology, your IO profiles are moved into the Container. This is very different than a file-based solution where we may be able to leverage streaming or similar technologies. Backend file service sizing is now critical as any negative performance will directly impact the user experience

**Availability:** Key to any service, and no different when dealing with FSLogix Containers, is the conversation around the availability of file services. Container technology is unforgiving when there are availability challenges – anyone who has lost a file server with mounted disks will understand what this pain looks like and how nasty bring the environment back to life can be.

Any decision around file services designed and implemented needs to capture availability requirements and associated considerations

**Cost:** Cost is, of course, going to play a significant part in any decision that is made. Not unexpectedly, the better the performance tier you want and need, the higher the dollar figure. However, investing in Storage is one of the best things you can do. Data is everything, reliable, performant and available storage is key to everything we do and consume

**Strategic Direction:** Existing strategies and architectural patterns will often govern which technology and solution you select for file services. For example, on a project with a number of Azure regions and a number of data centres with different underlying infrastructure technologies in each location, there was a strategic directive and requirement that the storage solution for containers in each location must be the same. This was an understandable requirement from a standards perspective, but severely limits what type of services you can consume, the decision was an easy one as there was only one clear path (we will touch on this later)

Many customers have a cloud-native requirement, meaning that if there is a cloud service that doesn’t require a Windows (or equivalent) service sitting in front of it churning up costs, then that is the architectural standard that must be taken

**Backup and Replication:** Data being key to everything, obviously leads to backup and replication capability of this data being a key consideration. Containers house user data and backup/replication strategies should be considered in line with the capability of each storage option. How do you access your backups, how do you access replicas in a disaster recovery or BCP scenario, how do you retain and govern the data sets? Do you even need to? All questions that have different answers depending on the solution, and all that will be specific to your organization

**Management and Maintenance:** Lastly, the management and maintenance of the file services environment shouldn’t be underestimated. There is a soft cost associated with complex or traditional file services solutions that can be mitigated by leveraging a slightly more expensive offering, this math is unique to each organization, but let's be honest, the simpler the solution, the less overhead for management, and the more native tooling available for support and admin staff to manage the solution, the better the result

**Hybrid Environments:** Not all environments are cloud-only. Many organizations strategically leverage Azure and other Cloud services as part of a burst strategy, or a needs basis. The Cloud platform may not be the primary location for hosting user workloads with the vast majority of workloads being leveraged on-premises. Containers being available in all locations that a user could possibly land in may be critical for making a true hybrid cloud environment a reality

In the context of Microsoft Azure, let’s look at the current storage options

## Windows File Servers

Microsoft Azure offers the everyday standard set of IaaS machines – or in the context of this topic: Windows File Servers. Traditional Domain joined servers, with disks attached and presented to the OS with file shares configured for general consumption.

A single node file server is simply a bad practice on-premises or in Azure, with technologies like Microsoft Windows Failover Clustering, Storage Spaces Direct (S2D) and Storage Replica being options to make Windows File Services highly-available in the context of Containers. [Leee Jeffries](https://twitter.com/leeejeffries) has some [great articles](https://www.leeejeffries.com/how-to-build-a-2-node-scale-out-file-server-for-highly-available-profile-storage/) on how to tackle Windows File Services HA on-premises, some of these concepts can be extended into Microsoft Azure.

Azure supports all of the above solutions in different ways, as well as a combination of them where required and appropriate. Like anything in Azure, this is also a changing space with new features being introduced regularly. For example, Managed Disks can now be consumed as a traditional shared disk required for a typical Windows File Server cluster as opposed to having to use a 3<sup>rd</sup> party offering or switch to S2D instead.

I recently worked on a project that had an initial architectural requirement _of "all locations, regardless of the platform or supporting technology must present file services in the same fashion and provide the same capability and availability"_. This was one of the numerous requirements that led to the decision to use Microsoft Windows-based file servers. The final solution consisted of [Storage Spaces Direct (S2D)](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview) clusters with [Azure Cloud Witness](https://docs.microsoft.com/en-us/windows-server/failover-clustering/deploy-cloud-witness), [Scale-out File Servers (SOFS)](https://docs.microsoft.com/en-us/windows-server/failover-clustering/sofs-overview) and cross-site cluster replication with [Storage Replica](https://docs.microsoft.com/en-us/windows-server/storage/storage-replica/storage-replica-overview). This was a solution that could be implemented regardless of the platform and location. It is based loosely off [this architecture provided by Microsoft](https://docs.microsoft.com/en-us/windows-server/storage/storage-replica/cluster-to-cluster-azure-cross-region).

File servers whilst functionally sound, proven and understood (you know what you are getting), are often not the most ideal deployment pattern for Cloud deployments, the overhead of windows, patching, management, licencing, running costs and anything else that ties into Windows Servers is something that we could all do without, which is why where possible, a PaaS offering is probably a nicer fit. Enter Azure Files.

## Azure Files

[Azure Files](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-introduction) is a Microsoft native File Services provisioned from Azure Storage Accounts. It is offered on a Standard or Premium tier and presents file shares in an almost identical fashion to a Windows File Server – a simple SMB endpoint. Azure files offer some additional fun with services like [Azure File Sync](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal) and [Azure Backup](https://docs.microsoft.com/en-us/azure/storage/files/storage-snapshots-files), but in the context of Containers, we really look at Security, IO and capacity.

Until recently, there was no native Active Directory integration for Azure Files, with either Azure Active Directory Domain Services being required to front the solution, or a shared access key based credential and [machine-based access mechanism](https://www.htguk.com/cloud-based-roaming-profiles-in-azure-with-fslogix-profile-containers/) in the form of startup scripts (or equivalent) for mapped drives being required to consume these services by your everyday Domain Users. This all changed recently with the release of [Azure Domain Join capability for Azure Files](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-identity-auth-active-directory-enable), now making the consumption of these services a true potential replacement for Windows file servers inclusive of security considerations.

[![Active Directory Domain Joined Azure Storage Account]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ADJoinedStorageAccount.png)]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ADJoinedStorageAccount.png)

Azure Files is perfect for Container workloads when considered and design appropriately, and for most organizations should do the job sufficiently. Its backups and snapshots are all native Azure capabilities and integrate nicely with the policy engines.

[![Containers in Azure Files natively]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ContainersInFiles.png)]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ContainersInFiles.png)

[![Windows Explorer view of Azure Files]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ExplorerView.png)]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ExplorerView.png)

You can integrate with [Azure Monitor](https://docs.microsoft.com/en-us/azure/storage/common/storage-monitor-storage-account) for performance metrics and alerting.

[![Azure Monitor for Storage Accounts]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/AzureMonitor.png)]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/AzureMonitor.png)

A challenge you may face is the IO to capacity scale, Azure Files scales IO capability alongside capacity provisioned for premium file shares (1 IOP per GiB), which may well lead to the requirement to distribute users across a number of different storage accounts. I have [a script here](https://github.com/JamesKindon/Citrix/blob/master/FSLogix/DistributeContainerShares.ps1) which supports Azure Files and may assist. There are few concepts to think about with the IOPS, baseline and burst IOPS (these are cool but hard to capacity plan against). Microsoft has a decent [explanation around the math](https://azure.microsoft.com/en-us/blog/premium-files-redefines-limits-for-azure-files/). TLDR: you have base IOPS, and for any IOPS you don’t use, you build credits, when you need to burst, you can draw down on those credits.

Additionally, there is only LRS capability for premium file shares, whereas Standard file shares support a range of replication models.

**Note!** For Azure Active Directory Domain Services integration to work in a supported fashion, you must deploy your Azure Storage Accounts into the same subscription that your Azure Active Directory Tenant lives (first pre-req [here](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-identity-auth-active-directory-enable) – don’t ignore it). Whilst we can have workloads in many subscriptions linked to a single Azure AD Directory, the actual storage accounts must live in the same subscription. If you do not, you will notice that the accounts will Join the appropriate Domain, they will also allow you to seamlessly sign on to share if you have the appropriate IAM permissions, but the NTFS security alterations will not be accepted at the share level. The mitigation here is to create a folder in the share, and then apply NTFS rights at that level. Your container pathing would then be something like `\\storageaccount\FSLogix\FSLogix`

[Christiaan Brinkhoff](https://twitter.com/Brinkhoff_C), of course, has a nice write up on the [end to end setup](https://www.christiaanbrinkhoff.com/2020/03/01/learn-here-how-to-configure-azure-files-with-active-directory-ad-authentication-for-fslogix-profile-container-and-msix-app-attach/).

## Azure NetApp Files (ANF)

Azure NetApp Files change the game significantly in Azure when thinking file services. It’s a fully managed and supported service from Microsoft, leveraging the brains of NetApp and Scale of Azure. It has native integration with Active Directory, and the scale capabilities are out of this world. If you have had previous experience with NetApp Filers, you will be familiar with simply using computer management to manage the shares – the same goes with ANF.

[![ANF Active Directory Integration]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANFDirectoryIntegration.png)]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANFDirectoryIntegration.png)

[![ANF Filer Management]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANFCompMgt.png)]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANFCompMgt.png)

[Marius Sandbu](https://twitter.com/msandbu) has a [nice write up from a little while back](https://msandbu.org/getting-started-with-azure-netapp-files/) and is still the best walkthrough on the web

You can mix Standard, Premium and Ultra Tiers of storage (Capacity Pools) and split your volumes up into either SMB based shares or NFS shares for high-performance storage for existing IaaS VMs. In the context of Containers, we simply need an Active Directory-integrated SMB share that can scale – Azure NetApp Files is perfect. Once available, it took me 10 mins to have a 4TiB file share available for containers.

[![ANF Capacity Pool]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANF.png)]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANF.png)

[![Windows Integration with ANF File Share]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANFExplorer.png)]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANFExplorer.png)

Backup is handled via NetApp snapshots natively within Azure, these can be automated with [this toolset](https://github.com/kirkryan/anfScheduler). The cool thing is you can get to your snaps directly in the shares, same as on-premises with NetApp Filers. Snap mirror technology is used to replicate cross-region where available, you won't be locked into mirroring premium to premium either, so costs can be controlled.

[![ANF snapshots]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANFSnaps.png)]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANFSnaps.png)

[![ANF Explorer Snapshot Access]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANFSnapsExplorer.png)]({{site.baseurl}}/assets/img/navigating-azure-storage-options-for-fslogix-containers/ANFSnapsExplorer.png)

The only downfalls at the moment for me are that costs start at [4TiB increments per capacity pool](https://azure.microsoft.com/en-au/pricing/details/netapp/). This may alienate smaller customers, however, for the larger side of town, the offering is going to make a whole world of sense.

You will need to plan for a delegated subnet to Azure NetApp Files and ensure this routable, but on a whole, this solution is awesome in its simplicity and promise for future growth. I have found the feedback and assistance from NetApp/Microsoft to be exceptional with their assistance and guidance, particularly with sizing estimates.

## Summary

File services are key to a well-performing container-based EUC environment, particularly FSLogix based containers. Performance, capacity, scale, backup, security and management are all critical components to consider when investigating what the right solution is. Hopefully, the above breakdown assists when looking into the options.

With a cloud-first hat on, I would suggest that Azure files be your first point of assessment, closely followed by Azure NetApp Files should you be a customer looking for scale and performance, particularly if you have a large number of file services to deal with. Azure IaaS offers the usual candidates, but it’s hard to get excited about IaaS in the face of PaaS.

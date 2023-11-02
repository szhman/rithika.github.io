---
layout: post
title: "Microsoft Azure Zone Architecture with Citrix Cloud"
permalink: "/microsoft-azure-zone-architecture-with-citrix-cvads/"
categories: [Citrix, CVAD, Cloud, Azure]
description: Designing, Implementing and Operating CVADS using an Azure Zone Architecture
image:
  path: /assets/img/microsoft-azure-zone-architecture-with-citrix-cvads/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Azure resiliency is a hot topic for any customer considering, or already operational within the Microsoft Azure ecosystem. The resiliency options are changing constantly (like everything in Azure to be fair) with a constant enhancement of availability and redundancy options available as the platform expands.

The focus of this articles is to delve into Availability Zone Architectural options and considerations for deploying highly available Citrix Cloud CVAD/DaaS solutions in Microsoft Azure.

Microsoft offers two native forms of high-availability and resiliency within Azure, [Availability Sets](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-availability-sets) and [Availability Zones](https://azure.microsoft.com/en-au/global-infrastructure/availability-zones/#overview). A nice quick overview of the two is referenced [here](https://techcommunity.microsoft.com/t5/itops-talk-blog/understanding-availability-sets-and-availability-zones/ba-p/1992518).

As defined by Microsoft:

> Azure availability zones are physically separate locations within each Azure region that are tolerant to local failures. Failures can range from software and hardware failures to events such as earthquakes, floods, and fires. Tolerance to failures is achieved because of redundancy and logical isolation of Azure services. To ensure resiliency, a minimum of three separate availability zones are present in all availability zone-enabled regions.
{:.per_microsoft}

> Each zone is composed of one or more datacenters equipped with independent power, cooling, and networking infrastructure. Availability zones are designed so that if one zone is affected, regional services, capacity, and high availability are supported by the remaining two zones
{:.per_microsoft}

## Designing for Citrix

When designing Citrix Cloud Virtual Apps and Desktops Service (CVADS or now DaaS) deployments with Azure Zones, we need to firstly define *why* we are using zones, and understand what protection a Zone Architecture gives us, vs that of a Regional architecture. The two are very different.

Azure ***Zones*** offer us multi location protection within a single ***Region***

[![Zones]({{site.baseurl}}/assets/img/microsoft-azure-zone-architecture-with-citrix-cvads/Zones.png)]({{site.baseurl}}/assets/img/microsoft-azure-zone-architecture-with-citrix-cvads/Zones.png)

Azure ***Regions*** are designed to offer protection against localized disasters and protection from regional or large geography disasters by making use of another ***Region***

[![Regions]({{site.baseurl}}/assets/img/microsoft-azure-zone-architecture-with-citrix-cvads/Regions.png)]({{site.baseurl}}/assets/img/microsoft-azure-zone-architecture-with-citrix-cvads/Regions.png)

Availability Zones are the more commonly preferred deployment model that I am seeing across my engagements globally now, with the majority of new builds opting for AZ architecture vs that of AS.

When thinking about CVADS components for an Azure Zone architecture, the following must be considered:

-  Citrix Cloud Connectors
-  Citrix StoreFront Servers
-  Citrix Federated Authentication Servers
-  Citrix Provisioning Servers (including database, storage and licencing)
-  Citrix Application Delivery Controllers
-  Microsoft Domain Controllers
-  Microsoft Certificate Authorities
-  User Data (File Servers, Azure Files)
-  User Profiles (File Servers, Azure Files, Azure NetApp Files)
-  Citrix Virtual Delivery Agents (workers)

Microsoft Azure provides a selection of core services and components, which are natively zone redundant. As it impacts a CVAD architecture, these services are:

-  Resource Groups
-  Azure Virtual Networks (VNETS) and associated subnets, network security groups etc

## Considerations for Zone Redundancy - Citrix Component Specific

**Cloud Connectors**, **StoreFront**, **FAS**, and **PVS** components are all low hanging targets for a Zone based deployment. Deploy one server role per Zone. 

**Do not forget** to consider sizing of instances. Failure of a zone, will result in reduction of of dispersed capability. No point having two small instances split across two active Zones, if one instance cannot handle the load of both in a failure scenario.

A note on **Citrix Provisioning Services**:

-  In Azure, it is supported to consume both Azure SQL and Azure SQL Managed Instance. It will be key to ensure the instances are provisioned with [Zone Redundancy configurations](https://docs.microsoft.com/en-us/azure/azure-sql/database/high-availability-sla?tabs=azure-powershell#general-purpose-service-tier-zone-redundant-availability)
-  If consuming Azure Files, It is a requirement to use premium storage accounts. Ensure Zone Availability within Region
-  There is **no support** for specifying specific availability zones for provisioned targets or for dedicated hosts
-  PVS still requires a licence server. This is likely best protected by ASR in a [Zone-to-Zone protection model](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery). Not all regions are supported

**Citrix Virtual Delivery Agents** provisioned by **Machine Creation Services** support Zone deployments, there are a couple of considerations of note when considering zone architecture. MCS in Azure allows the selection of one, two or three zones within a catalog

-  When specifying a single zone, all workloads are deployed in that zone
-  When selecting multiple zones, workloads are distributed randomly across all selected zones
-  When no zones are selected, MCS takes no zone placement into consideration and deploys wherever Azure allows it to

It is possible to retrofit zones into an existing catalog or change the zone assignments of an existing zones based catalog as requirements change.

The current MCS architecture means there are two key deployment models for Zone based provisioning:

-  A catalog-to-zone assignment. This is similar to MCS on AWS architecture and specifies a unique catalog per zone, with a single Delivery Group to aggregate multiple. This has double or triple the image release overheads from a deployment standpoint, but gives full control of zone placement for provisioned workloads
-  Deploy a catalog with multiple zones defined and allow non-controlled distribution across the zones (you hope the machines are dispersed evenly)

Simple is typically better, the latter option is what I tend to lead with in design workshops, however requirements will define your decisions.

Currently, Citrix MCS deploys the preparation VM used to prep the image with an awareness of Zones based on the catalog configuration. This means that if there are capacity issues within a zone, your prep machine can, and will be impacted.
{:.warning}

**Citrix FAS** has a direct dependency on **Microsoft Certificate Authorities** to provide user certificates. I like the architecture of combining each FAS server with a subordinate issuing CA role, allowing each box to act independently, and also provide high availability for each component with a crossover logic (FAS server 2 is a secondary issuing CA for FAS server 1).

This architecture leads to simple supporting Azure Zone patterns. If not following this model, then you will need to ensure CA architecture is Zone redundant and ideally FAS preference is for its "same zone CA" with a failover to the second or third zone.

**Citrix ADCs** in Azure are simply virtual machines. A native Zone based template is provided by Citrix, ensuring that all components are deployed at an appropriate SKU to support zone deployments (Azure Load Balancers, IP addresses etc), however this template is HA pair based, meaning that you are limited to **two** zones which is a slight issue if you are designing a 3-zone resiliency model.

These templates are located on the [Citrix Github](https://github.com/citrix/citrix-adc-azure-templates/tree/master/templates/high_availability) repository for reference.

If your Azure zone architecture requires a 3-zone resiliency model, then GSLB across 3 individual ADC's (one per zone) may be the best suited deployment model.

Conversely, GSLB architecture can tend to make more sense from a cost perspective given that a passive node in a HA deployment is rarely used. GSLB offers a decent alternative to driving more bang for buck when focused on overall resiliency and active-active ADC capability.

Finally, native ADC GSLB isn't the only option for providing failover and load balancing across the ADC's. [Azure Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-overview) is an excellent option for simple DNS based load balancing across ADC's in Azure. It works a treat and offers some very advanced capability with a very simple deployment mode.

## Considerations for Zone Redundancy - Citrix Component Supporting

Microsoft components such as **Domain Controllers** are also simple targets for a "server per zone", given many Citrix architects are designing against an existing Azure environment, it is important to ensure that supporting roles are zone redundant and calling out the pitfalls if they are not.

**User data** and **profiles** are a bit of fun. Options and considerations will change depending on what storage solution is chosen to run with in Azure, some considerations below.

**Windows File Servers** in Azure are still common. File server clusters are also an available option, but designing these with Zone redundancy in mind will likely mean reading through the fine print and ensuring your ducks are aligned.

The simplest approach if using file servers may well be to forget a cluster, and move to single instance file servers, zoned (both VM and Managed Disk) with replication (bvckup2, Azure File Sync, Robocopy) and DFS-N at the front.

There is a new [Zone-Redundant disk option](https://docs.microsoft.com/en-us/azure/virtual-machines/disks-deploy-zrs?tabs=portal) in Azure for managed disks. This works similarly to storage accounts in that the disk is automatically replicated across three zones behind the scenes. You do not need to specify the zone. Unfortunately, as with many services, this is not available in all regions (including Australia dammit) and you need to be cognizant of how this fits together and it's limitations.

**Azure Files on Azure storage accounts** are by default configured as locally redundant, meaning that data is replicated 3 times within a Zone. A conscious decision is required to enable z[one-based redundancy (ZRS) on storage accounts](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy#zone-redundant-storage ) supporting file shares for FSLogix, Citrix Profiles, Layers etc.

With ZRS, storage accounts automatically resume service in the event of a zone outage. Ensure that you understand storage account types and zone capability when designing or implementing Azure Files. Also ensure that your region supports the capability – not all do.

For **Azure NetApp Files** I have found it next to impossible to find anything specifying how ANF is deployed, one can only assume that it is natively zone redundant, however I will update this post when I get clarity or confirmation.

## Considerations for Zone Redundancy - Azure

**Quotas and Availability**. Interestingly, different Zones may not always have the same capacity nor SKU availability. I don't know how widespread this is, but I have seen instances where capacity limits or failures are hit more often in a specific zone.

I have also seen instances where specific GPU instance sizes are available in only two out of the three zones at play. If you have a Microsoft rep, it may be worth touching base and understanding if there are Zone capacity considerations based on your deployment regions prior to jumping into the fire with changes.

**The weakest Link**. The strongest architecture is only as good as its weakest link, and no matter how resilient we can design a CVADS solution, it has always been, and will always be, at the mercy of the supporting infrastructure. If there are weak links in the chain, then the whole exercise is likely fruitless, some considerations that come to mind:

-  Network Virtual Appliances (NVA) such as firewalls etc
-  Storage for user data
-  Directory Services, Identity components, core application servers, database and web servers
-  Network terminations and connectivty back to on-premises services

## Migrating to Zone Architecture

Many customers may have started with an Availability Set, or even no redundancy configured on certain roles within Azure and may consider moving to zone architecture as part of an overarching strategy – this is not as simple as one might think.

There is no way to simply change a VM's zone once that VM is deployed. Additionally, it is not just the VM that needs to be thought through, its corresponding disk or disks need to be aligned to the appropriate zones.

The process typically involves blowing away the existing VM and Disk, redeploying a new one of each in the appropriate Zone, and then moving on with life. Lucky enough, there is PowerShell. I have written a small but powerful script which will take an existing VM and its associated disks and redeploy them into a zone of your choice. The script also supports Citrix ADC via the `*-IsADC*` switch. This switch retrieves additional required attributes for marketplace details.

If you are migrating an ADC, you must ensure that ALL components are of a supported SKU. You cannot have any `Basic` tier components in the mix (Load balancers or public IP's). These must be of a `Standard` Sku.
{:.warning}

You can [view the code here](https://github.com/JamesKindon/Azure/blob/master/ChangeVMAz.ps1). Feel free to add, update or critique as required.

## POD operational architecture with Zones – "the art of the possible"

Designing Citrix solutions with "pod" or "modular" architectural principals is always a good idea. A pod architecture is the simple concept of a collection of building blocks or functions that operate together. To scale the solution, you simply add another collection of resources; a "pod".

This principal of this design typically aligns with a rule that you want each pod to operate independently. This is very similar to the approach that Microsoft took with zone principals in Azure.

The typical layered approach that Citrix uses means that realistically, we are focused on the Resource and Control layers when thinking about pod architecture. The Access layer is typically going to be delivered by Workspace and Gateway Service (in a perfect world) or StoreFront and ADC, which are both (if architected properly) extremely resilient to the loss of a node, meaning that active-active across these solutions is likely the best path forward.

So how do we align an Azure zone architecture, with a CVAD pod architecture, and attempt to have all services within a zone operate directly with services in the same zone? There are a range of considerations and design principals to make this a reality, a starting point is listed below.

I will caveat this with it's theoretical for most part, and just a brain tickler – just because we could, doesn't mean we should....

-  An infrastructure role for each service per Zone (as discussed above) with a preference for services intra-zone to leverage the local Zone role first
-  Confirmation that your chosen instance types are available across all zones. Following this, MCS provisioning leveraging a zone-to-catalog design. This is going to be highly taxing from an operational management overhead, as three catalogs would equal three updates tasks. To manage this, I would suggest using the CVAD Service APIs to automate the task
-  A naming convention per zone (include a zone reference in your names) so that you apply policy filtering for:
    -  VDA registration including [registration groups](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/manage-deployment/vda-registration.html) for example: Zone 1 VDA registration to Zone 1 Cloud Connector
-  An Active Directory OU structure to support group policy deployment and ordering based on Zone pods
    -  Citrix FAS ordering which will [impact StoreFront](https://support.citrix.com/article/CTX225721). A far more insightful read from [Michael Shuster](https://www.linkedin.com/in/iammichaelshuster/) at [Ferroque Systems](https://twitter.com/FerroqueSystems) outlines [this in detail](https://www.ferroquesystems.com/resource/howto-active-active-multi-datacenter-citrix-fas/)
-  Users identified to operate in a specific zone, this would be similar to a home zone logic within the CVAD realm

## Summary

Citrix and Azure is a lot of fun, and a constantly evolving solution stack. Availability is critical to any good functional and operational design, zone architecture plays a large part in this stack however needs to be thought through end to end to achieve the full benefit.

Key summary points

-  Decide on Zone architecture standards before designing Citrix
-  Ensure the chosen Azure Regions support Zones
-  Ensure each zone supports the appropriate instance sizes
-  Ensure each Region and Zone supports the appropriate level of redundancy on supporting infrastructure components
-  The strongest architecture is only as good as it's weakest link

I am very keen for this to be an open conversation, so if there is constructive feedback, commentary of additional things that I have missed (there will be plenty), feel free to leave a comment and I will amend accordingly.

Next stop, regional deployment considerations.

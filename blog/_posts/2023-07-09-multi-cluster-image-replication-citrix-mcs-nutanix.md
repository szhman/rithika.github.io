---
layout: post
title: Multi Cluster Image Replication for Citrix MCS on Nutanix
permalink: "/multi-cluster-image-replication-citrix-mcs-nutanix/"
categories: [Citrix, MCS, Provisioning, PowerShell, Nutanix, HCI, Prism]
description: Automating The Replication of Citrix MCS Images on Nutanix
image:
  path: /assets/img/multi-cluster-image-replication-citrix-mcs-nutanix/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Nutanix and MCS Provisioning

Many Nutanix customers utilise Citrix Machine Creation Services (MCS) for their workload provisioning. Nutanix with MCS is a beast mode option for performance and simplicity and is common for customers looking to simplify their Citrix environments.

Currently, our integration point with Nutanix for both Citrix DaaS and Citrix VAD is at the Prism Element level. Prism Element is of course a cluster-level management construct, so for each Cluster that is used for workload provisioning, a Hosting Connection and at least one Catalog associated with that hosting connection is required.

This construct also means that customers may need a replica of the master/base/gold image on each cluster that they wish to provision workloads on. Because we are currently using a Prism Element integration logic, this image is presented to Citrix in the form of a traditional Snapshot.

The requirement to have the same snapshot available across multiple clusters is easily addressed by several different Nutanix capabilities, however, Is often an additional manual step in the image release cycle.

These processes can of course, like most things in our world, be automated, and depending on the customer deployment/requirement there are several paths that can be taken. We document these options on our Solutions Portal, specifically for this scenario, we provided a technote: [Citrix Base Image Migrations and MCS Considerations on Nutanix AHV](https://portal.nutanix.com/page/documents/solutions/details?targetId=TN-2181-Citrix-Base-Image-Migrations-and-MCS-on-Nutanix-AHV:TN-2181-Citrix-Base-Image-Migrations-and-MCS-on-Nutanix-AHV).

In addition, there are a couple of cool "art of the possible" projects that we have released via the awesome [Nutanix.dev](https://www.nutanix.dev/) portal.

## Replicating Images for Citrix MCS with Prism Element

![project1]({{site.baseurl}}/assets/img/multi-cluster-image-replication-citrix-mcs-nutanix/citrix_mcs_rep_pe.png)

Project number one: [Replicating Images for Citrix MCS with Prism Element (Protection Domains and PowerShell)](https://www.nutanix.dev/2023/07/03/replicating-images-for-citrix-mcs-with-prism-element-protection-domains-and-powershell/) addresses replication of snapshots via Nutanix data protection in the form of Protection Domains. There are also two methods used to automate this process each with its own script example:

-  Nutanix PowerShell Cmdlets (v1)
-  Nutanix v2 APIs (Prism Element)

## Replicating Images for Citrix MCS with Prism Central

![project2]({{site.baseurl}}/assets/img/multi-cluster-image-replication-citrix-mcs-nutanix/citrix_mcs_rep_pc.png)

Project number two: [Replicating Images for Citrix MCS with Prism Central (Protection Policies and Recovery Points)](https://www.nutanix.dev/2023/07/04/replicating-images-for-citrix-mcs-with-prism-central-protection-policies-and-recovery-points/) Addresses a scenario where customers may use Prism Central to manage multiple clusters and may wish to use Protection Policies to handle the replication of snapshots (Recovery Points in the PC world) across those clusters.

## Summary

Both projects offer not only an example of how to automate the replication of snapshots across multiple clusters in a consistent fashion but also uses (for CVAD) the Citrix PowerShell Snapins to automate the upgrade of one or many Catalogs, across one or many Citrix Sites. This logic can easily be extrapolated to DaaS deployments using the DaaS SDK as required.

Check them out, the code is [housed in Github](https://github.com/nutanixdev/euc-samples/tree/main/citrix/mcs) and free to take, enhance and use accordingly per our [documented terms of use](https://www.nutanix.com/legal/terms-of-use), with documentation, background and associated details outlined for each project mentioned above at the following URLS

-  [Replicating Images for Citrix MCS with Prism Element (Protection Domains and PowerShell)](https://www.nutanix.dev/2023/07/03/replicating-images-for-citrix-mcs-with-prism-element-protection-domains-and-powershell/)
-  [Replicating Images for Citrix MCS with Prism Central (Protection Policies and Recovery Points)](https://www.nutanix.dev/2023/07/04/replicating-images-for-citrix-mcs-with-prism-central-protection-policies-and-recovery-points/)

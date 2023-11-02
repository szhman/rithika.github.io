---
layout: post
title: Managing Nutanix Prism Central Categories for EUC workloads
permalink: "/managing-nutanix-categories-powershell/"
categories: [Citrix, MCS, Provisioning, PowerShell, Nutanix, HCI, Prism, Categories]
description: Automating the assignment of Nutanix Categories with PowerShell
image:
  path: /assets/img/managing-nutanix-categories-powershell/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Nutanix Prism Central Categories

Much of the advanced capability within Nutanix Prism Central is implemented with Categories being the key to targeting capability to the appropriate workload. There are a range of services impacted by Category assignments, some examples being:

-  [Protection Policies](https://portal.nutanix.com/page/documents/details?targetId=Disaster-Recovery-DRaaS-Guide-vpc_2023_1_0_1:ecd-ecdr-draas-management-protectionpolicy-pc-c.html)
-  [Flow Network Segmentation policies](https://portal.nutanix.com/page/documents/solutions/details?targetId=TN-2094-Flow:TN-2094-Flow)
-  [Calm Workflows](https://portal.nutanix.com/page/documents/details?targetId=Nutanix-Calm-Admin-Operations-Guide-v3_6_2:nuc-blueprints-tags-categories-overview-c.html)
-  [Self Service Capability](https://portal.nutanix.com/page/documents/details?targetId=SSP-Admin-Guide-v6_6:SSP-Admin-Guide-v6_6)
-  [X-Play](https://www.nutanix.dev/2022/01/19/getting-started-with-nutanix-x-play/)
-  [Storage Policies](http://%28https/www.nutanix.dev/2023/01/30/entity-centric-storage-policies/)

Many Citrix environments will use Citrix Provisioning Services or Citrix Machine Creation Services to quickly deploy thousands of virtual machines. These environments may require Categories to be applied to these virtual machines post provisioning.

Nutanix provides a way to automate the management of tags via the [Prism v3 API for Prism Central](https://www.nutanix.dev/api_references/prism-central-v3/#/4b402d5ab3edd-introduction), so naturally, we can automate the process at scale with PowerShell.

Check out a [script example and write up](https://www.nutanix.dev/2023/07/26/managing-categories-for-euc-workloads-in-prism-central/) over on nutanix.dev. The code is [housed in Github](https://github.com/nutanixdev/euc-samples/tree/main/citrix/categories/manage_pc_vm_categories) and free to take, enhance and use accordingly per our [documented terms of use](https://www.nutanix.com/legal/terms-of-use), with documentation, background and associated details outlined
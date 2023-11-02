---
layout: post
title: "Automating Citrix Persistent VM Migrations across Nutanix Clusters"
permalink: "/nutanix-citrix-persistent-vm-migration/"
description: "Migrating workloads across clusters and retaining Citrix power management"
categories: [Citrix, Nutanix, VDI, DaaS, EUC, PowerShell]
image:
  path: /assets/img/nutanix-citrix-persistent-vm-migration/post_default_image.jpg
sitemap: true
hide_last_modified: false
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Many Nutanix customers deploy persistent workloads on Nutanix infrastructure. These workloads may be Windows Client (Windows 10 or Windows 11) desktops, or they could be custom-built Windows Server (2019, 2022 etc) based Operating Systems.

Regardless of how a workload is built (note that we are not talking about MCS in this article), integrating it with a Power-Managed Catalog in Citrix allows administrators and support folk to manage the machine within both Citrix Web Studio and Citrix Director.

Currently, Citrix VAD & DaaS integration with the Nutanix platform is via Prism Element.

The integration for each workload is made up of two components:

-  A Hosting Connection Identifier (`HypervisorConnectionUid`). This tells Citrix  which Hosting Connection each workload is associated with, or in human speak: "Where the workload lives".
-  A Hosted Machine Identity (`HostedMachineId`) identifies the virtual machine at the hypervisor layer. In the Nutanix world, this unique identity matches the unique virtual machine `UUID` in the Nutanix AHV hypervisor.

There are scenarios where these records can become out of sync and a workload can be orphaned:

-  A customer implements a new Nutanix cluster and moves workloads across to it.
-  A customer activates a disaster recovery scenario, and the workloads are activated on another cluster.

In either of these scenarios, without intervention, Citrix  no longer understands where the workload resides and in some scenarios such as in a Protection Domain Activation, how to identify the workload as its `UUID` will have changed.

## The Solutions

We have released two art-of-the-possible scripts to show how you can handle this transition of workloads across clusters whilst maintaining full visibility within Citrix.

For CVAD we leveraged Citrix Powershell Snapins and Nutanix v2 API with PowerShell and for DaaS we leveraged Citrix Cloud DaaS API and Nutanix v2 API with PowerShell.

Both projects are another example of zero touch configuration with Nutanix and Citrix technology. You can read up on the process and details below:

-  [Persistent VDI Migration with Citrix VAD](https://www.nutanix.dev/2023/09/28/persistent-vdi-migration-with-citrix-vad/)
-  [Persistent VDI Migration with Citrix DaaS](https://www.nutanix.dev/2023/09/29/persistent-vdi-migration-with-citrix-daas/)

A shortcut to the scripts can be found below:

-  [Reset Hosted Machine ID Citrix VAD](https://github.com/nutanixdev/euc-samples/tree/main/citrix/multi_cluster_migration/citrix_cvad_reset_hostedmachineid)
-  [Reset Hosted Machine ID Citrix DaaS](https://github.com/nutanixdev/euc-samples/tree/main/citrix/multi_cluster_migration/citrix_daas_reset_hostedmachineid)
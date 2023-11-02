---
layout: post
title: "Azure Cost Optimization with Citrix DaaS"
permalink: "/azure-cost-optimisation-with-citrix-daas/"
subtitle: "Tips and Tricks to reduce Azure costs with Citrix DaaS capability"
categories: [Citrix, Optimization, Azure, Cost, MCS, DaaS, Cloud, IaaS]
description: Tips and Tricks to reduce Azure costs with Citrix DaaS capability
image:
  path: /assets/img/azure-cost-optimisation-with-citrix-daas/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Let's face it, Azure isn't cheap, and it isn't getting any cheaper. EUC workloads are getting heavier and their requirements larger, all of this means you are either sacrificing user experience on sub-par instances, or forking out the dollars to keep end users happy.

Luckily there are some easy wins to try and make a small dent in that bill. Below is my tracking list based on projects and ongoing capability releases in the Citrix DaaS offering, mostly around the MCS space.

## Category: Storage

**Identity Disks**

Identity disks are low hanging fruit, and in modern deployments of Citrix DaaS should no longer be a subject of concern as MCS now uses standard SSD for everything. However, if you have not been watching, or have some older deployments, have a read on the [impact/considerations of identity disks with Citrix MCS](https://jkindon.com/enhancing-citrix-mcs-and-microsoft-azure-part-1-identity-disk-cost-optimization/)

**Ephemeral Disks**

Another storage focused win, this one very cool because you get to bundle massive performance gains along with considerable reduction in disks for MCS workloads. If you have been under a rock, [you can read more about it here](https://jkindon.com/citrix-mcs-and-azure-ephemeral-disks/) (I really like this one)

**Change the Storage Tier when VM is shut down**

Standard HDD spec disks really don't create the nicest experience for users, so most deployments opt for a better SKU (Standard or Premium SSD). But when a machine is turned off (see AutoScale and Power Management note), why pay for the higher Tier? With MCS, you can switch the OS disk to a lower tier (Standard HDD) and take the win. MCS will switch it back when the instance powers on.

[This feature](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage/manage-machine-catalog-azure.html#change-the-storage-type-to-a-lower-tier-when-a-vm-is-shut-down) is most commonly of benefit in persistent/dedicated instance deployments, however will also work if persisting OS disks (by default MCS uses [on demand provisioning](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#azure-on-demand-provisioning), so no disk or VM when the instance is off). Also works with persistent write back cache (but if you have read up on Ephemeral Disks then this likely won't be relevant)

**Snapshot Maintenance**

MCS uses Azure Snapshots for catalog updates. Azure only charges for used space for the snapshot as opposed to a provisioned size, but if you retain lots of snapshots, then that cost starts to add up. Review snapshots regularly and consider Azure Automation jobs to both report on, and delete snapshots based on acceptable criteria.

**Profile Data**

Who loves large containers (FSLogix) living on Azure Files Premium Shares? Microsoft does, your wallet does not. Premium File shares are billed on provisioned capacity and whilst Microsoft have reduced pricing every so often, storage still makes up a significant portion of an azure bill in the EUC space.

Ensuring that profile data is maintained will directly impact your storage size requirements, and thus the bill you get. Take a look at [profile pruning](https://stealthpuppy.com/fslogix-containers-capacity/), or container compacting tasks to keep things nice and lean. Additionally, ensure decent offboarding processes to remove data once users eject from the business.

**Microsoft Defender for Storage**

This one makes the list as more of a "watch out" item. This thing will kick you right in butt from a cost standpoint. I have seen customers security teams turn this on because it says "security" on the box. Uh oh.

## Category: Instance Management

**Citrix Autoscale**

[Citrix Autoscale](https://docs.citrix.com/en-us/citrix-daas/manage-deployment/autoscale.html) is one the best and most powerful tools to get your VM run costs under control. Coupling Autoscale with aggressive session timeouts can massively reduce your run costs.

Don't forget to plug in your instance $$ figure into your Delivery Group so that you can get some nice reporting in Monitor on how much you are saving (VM run cost + Disk cost should be added if using [on-demand provisioning](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#azure-on-demand-provisioning))

**Instance Reservation**

This one can be massive depending on the use of DaaS workloads. [Reserved Instances](https://azure.microsoft.com/en-us/pricing/reserved-vm-instances/) allow a pre-commit to consume the instance over a 1- or 3-year period. This typically offers a massive reduction in cost for the instance itself.

In some scenarios, where DaaS workloads run for extended periods of time, Reserved Instances can offer greater cost gains than Autoscale.

In other scenarios, a combination of both Reserved Instances and Autoscale can be beneficial, where the baseline (minimum) number of instances to serve the business can be reserved, and then Autoscale can handle the rest on a PAYG rate.

Remember, [Instance Reservations float](https://learn.microsoft.com/en-us/azure/cost-management-billing/manage/understand-vm-reservation-charges?toc=%2Fazure%2Fcost-management-billing%2Freservations%2Ftoc.json#how-reservation-discount-is-applied), they aren't tied to a specific VM, so if Autoscale powers off a VM with a reservation associated to it (and MCS is using on-demand provisioning and the VM is destroyed), the benefit can just be moved across to another VM that is still running.

Build an Excel sheet and map out run time, Reserved Instance (1 and 3 year options) pricing and see what your costs can look like – it will be surprising when you start to mix and match.

If not using on-demand provisioning, or choosing to persist OS Disks which are premium, [Reserved Instances for Managed Disks](https://learn.microsoft.com/en-us/azure/virtual-machines/disks-reserved-capacity) may also offer some cost benefits.

**License/HUB**

BYOL/[Hybrid Use Benefit (HUB)](https://azure.microsoft.com/en-us/pricing/hybrid-benefit/) offers monster gains under the right conditions. If you have an appropriate agreement to bring your own Windows Licence or consume HUB, do not forgot the tick the box accordingly – else, you will be paying a nice amount of $$ to Microsoft for Windows licencing as part of your instance costs.

Citrix DaaS MCS allows [provisioned machines to be tagged](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html) with either a "Windows Server License" for Server OS or "Windows Client License" for single session desktop OS (Windows 10 or 11. There are also considerations for Redhat and Suse Linux.

**Citrix VDI Reclamation Service**

A smart way of using the Citrix [automated tagging](https://www.citrix.com/blogs/2023/05/23/reduce-your-cloud-costs-with-citrix-vdi-reclamation-service/) feature to build your own service to identify and reclaim orphaned VDI.

## Category: IaaS

**Erroneous backup jobs**

Backups being either native or 3rd party, can have an impact on ongoing costs. Some use snapshots in Azure, whereas the native service uses its own mechanism. Both have cost implication, so making sure that backups are strategic is important.

In the Citrix world, there is no need to backup Cloud Connectors for example. These aren't supported in a restoration scenario, and can be very quickly rebuilt. So think about the use in backing them up.

**Single vs Multi Session economics for VDI/DaaS workloads**

This one can be a fun path to navigate, however the long and short is typically this: Instance sizing in public cloud tends to give you better bang for buck in a multi-session scenario.

The lowest economic value on the Azure fabric natively is typically going to be a single session OS with a single user. This is not always the case, but in most circumstances, there can be significant savings in combing 3 or 4 people onto an instance using Multi Session OS (Server or Client) rather than having 3-4 instances serving users in a 1:1 scenario. Model it, do the math, your milage may vary.

This does not hold true when using bare metal solutions such as Nutanix NC2, where you have full control over your tin, and are not locked into T-Shirt sized instances. In this scenario, it's game time, you can do everything you do on-prem, but do it in public cloud. Different conversation.

**Identify MCS orphaned resources on Azure**

You can retrieve a list of [orphaned resources](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage.html#retrieve-a-list-of-orphaned-resources) that are created by MCS but are no longer used by MCS.

## Summary Table

| Category | Item | Guide |
| :-- | :-- | :-- |
| Storage | Identity Disks | Correctly sized disks = Less $$ |
| Storage | Ephemeral Disks | Less disks = Less $$ |
| Storage | Change the Storage Tier when VM is shut down | Lower Sku = Less $$ |
| Storage | Snapshot Maintenance | Less Snapshots = Less $$ |
| Storage | Profile Data | Less data = less storage = Less $$ |
| Instance Management | Citrix Autoscale | Less Running Instances = Less $$ |
| Instance Management | Instance Reservation | Less PAYG rates = Less $$ |
| Instance Management | License/HUB | More BYOL or HUB = Less $$ as far as Azure spend goes |
| Instance Management | VDI Reclamation Service | Less orphaned crud = Less $$ |
| IaaS | Erroneous backup jobs | Less (useless) backups = Less $$ |
| IaaS | Single vs Multi Session economics for EUC workloads | Right workload = Appropriate $$ |
| IaaS | Identify MCS orphaned resources on Azure | Less orphaned crud = Less $$ |

## Summary

This list is not exhaustive, nor is it gospel. Hopefully it helps those in the field, and if I have missed anything obvious, feel free to share and we can add it to the list.
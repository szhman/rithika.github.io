---
layout: post
title: "Migrating Citrix Persistent Workloads Across CVAD Sites and DaaS"
permalink: "/citrix-migrate-dedicated-workloads/"
description: "Yet another set of migration scripts for persistent VDI workloads"
categories: [Citrix, Cloud, VDI, DaaS, PowerShell]
image:
  path: /assets/img/citrix-migrate-dedicated-workloads/post_default_image.jpg
sitemap: true
hide_last_modified: false
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Migration seems to be the theme of my scripting over the last few months, the world of persistent VDI workloads is an extensive one.

Conversely, the title image of the post is a bunch of camels walking across a desert. Far stretch to associate a camel with a VM migration - but I guess you kinda could? Anyhoo, I amuse myself 😂 onwards to relevance.

Years back I put together some very basic scripts that allowed the migration of Persistent VDI workloads from an on-premises CVAD deployment to Citrix Cloud DaaS. These scripts did their job but had some glaring holes in them (which happens when you write them onsite...) so I figured I would clean them up. Which of course, meant re-writing, error handling, adapting for changes in the Citrix landscape and crosschecking a few more bits and bobs.

Pipe down and get to the point? Fair play. Two new scripts below:
{:.tldr}

## MigrateDedicatedMachines.ps1

The script is designed to move a dedicated single-session machines from one CVAD site to another whilst retaining full power management. It does the following:

-  Queries a Source Delivery Controller (CVAD) for dedicated machines. This can be either via a Catalog, or a list of machines that you specify.
-  Queries a Target Delivery Controller (CVAD) to understand target Catalog, Hypervisor Connection, Delivery Group etc. If you forget to provide these, you will be presented with an appropriate list of candidates.
-  Sanity checks a whole load of things to make sure that hypervisor types are the same, target Catalogs are not of a provisioned type, source machines are not PVS provisioned etc.
-  Executes a migration from Site 1 to Site 2 handling:
    -  Machine Object migration and lineup with hosting
    -  Catalog and Delivery Group Assignments
    -  User Assignments
    -  Published Name Assignments via a range of different configurable options to assist with user continuity
    -  Optional Hosting Connection resets to avoid delay of power status updates
    -  Optional Maintenance Mode configuration for either source or target
    -  Optional VM source removal for both MCS and Manual workloads (see MCS considerations Section)

This script uses 100% Citrix PowerShell snapins. As such, you are stuck at PowerShell 5.1. You will need to execute on a machine where you have appropriate connectivity and access to both sites. 

If using the MCS migration options, read the fine print in the [readme](https://github.com/JamesKindon/Citrix/blob/master/Migration%20Scripts/MigrateDedicatedMachines/MigratedDedicatedVM/README.MD#a-note-on-mcs-source-machine-removal) and the [note below](#mcs-considerations).
{:.special_note}

You can find the script in the usual github repo [MigrateDedicatedMachines.ps1](https://github.com/JamesKindon/Citrix/tree/master/Migration%20Scripts/MigrateDedicatedMachines/MigratedDedicatedVM). Also linked to in the [Script Index](https://jkindon.com/ScriptIndex/) section of my site.

## MigrateDedicatedMachinesDaaS.ps1

Similar to the above script, this one is designed to move dedicated single-session machines from one CVAD site to Citrix Cloud DaaS whilst retaining full power management. It does the following:

-  Queries a Source Delivery Controller (CVAD) for dedicated machines. This can be either via a Catalog, or a list of machines that you specify.
-  Queries Citrix Cloud (DaaS) to understand target Catalog, Hypervisor Connection, Delivery Group etc. If you forget to provide these, you will be presented with an appropriate list of candidates.
-  Sanity checks a whole load of things to make sure that hypervisor types are the same, target Catalogs are not of a provisioned type, source machines are not PVS provisioned etc.
-  Executes a migration from CVAD to DaaS handling:
    -  Machine Object migration and lineup with hosting
    -  Catalog and Delivery Group Assignments
    -  User Assignments
    -  Published Name Assignments via a range of different configurable options to assist with user continuity
    -  Optional Hosting Connection resets to avoid delay of power status updates
    -  Optional Maintenance Mode configuration for either source or target
    -  Optional VM source removal for both MCS and Manual workloads (see MCS considerations Section)

This script uses a combination of Citrix PowerShell snapins for the CVAD components and Citrix Cloud APIs for the DaaS components. As such, you are stuck at PowerShell 5.1. You will need to execute on a machine where you have appropriate connectivity to the source CVAD Site and internet access to the Citrix Cloud environment. Authentication is best handled by DaaS using a [Secure Client File](https://docs.citrix.com/en-us/citrix-cloud/sdk-api.html).

If using the MCS migration options, read the fine print in the [readme](https://github.com/JamesKindon/Citrix/blob/master/Migration%20Scripts/MigrateDedicatedMachines/MigrateDedicatedVMDaaS/README.MD#a-note-on-mcs-source-machine-removal) and the [note below](#mcs-considerations).
{:.special_note}

You can find the script in the usual github repo [MigrateDedicatedMachinesDaaS.ps1](https://github.com/JamesKindon/Citrix/tree/master/Migration%20Scripts/MigrateDedicatedMachines/MigrateDedicatedVMDaaS). Also linked to in the [Script Index](https://jkindon.com/ScriptIndex/) section of my site.

## MCS Considerations

Previously I have typically written specific exclusions for MCS provisioned workloads, but with these scripts, I am including the ability to include MCS full cloned workloads as source machine candidates, and include the ability to clean up the source MCS catalogs once the machine has migrated successfully. This is an optional component that you need to enable via the appropriate parameters.

If you are using a newer version of the Citrix PowerShell Snapins than your site is currently running, the `-ForgetVM` switch may not operate as documented, and the VM entity will be removed/deleted (yes deleted) from the hypervisor ☠️.
{:.disclaimer}

This has been validated as a bug in scenarios such as Site Version: `2203 LTSR` and Studio/PowerShell Version `2305` on a remote server. It is wise to keep operational components at a similar version.

Test before executing production workloads (scope with a `MachineList` specific command set)

## Summary

These scripts are 100% Citrix component only focused. You will need to think about VDA registration and switching OU locations etc to get policies. All very easy with AD PowerShell modules.

These scripts have been tested with as many different platforms as I can get my hands on, but I don't have access to them all. Always use the `-whatif` switch to identify what the script will do prior to executing. And test first.

Anything you want to fix - feel free to submit a pull request, or shoot me the detail. In particular, Hyper-V is a dark spot.

## FAQ

Cool story, but you know Citrix has the automated configuration tool right?
{:.why}

Indeed. But this is way more granular and gives you some funky features that ACT does not. ACT is also limited to DaaS and CVAD current releases with the orchestration APIs in preview.

Why not just use the Remote PowerShell SDK for DaaS and save yourself the heartache of API mapping
{:.why}

That would be boring, and combining the Remote PS SDK with the On-prem one is a nightmare. API for DaaS it is.

Did anyone actually ask you these questions?
{:.why}

Absolutely not.

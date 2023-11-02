---
layout: post
title: "Replicating Azure Snapshots to Multiple Azure Regions"
permalink: "/microsoft-azure-snapshot-replication-framework/"
categories: [Citrix, Azure, PowerShell, Automation, CVAD, Cloud, MCS]
description: Replicating Azure Snapshots to Multiple Azure Regions
image:
  path: /assets/img/microsoft-azure-snapshot-replication-framework/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->
-  Table of Contents
{:toc}

## Intro

Multi-region deployments in Microsoft Azure are often part of a highly-available Citrix Cloud DaaS implementation. One of the challenges associated with this style of deployment is ensuring that you have a replica of your images available to spin up catalogs based on a local image source rather than relying on MCS trying to push your image regionally (which can be done but is mighty slow and failure can result in lengthy catalog update processes).

Yes, there is of course the [Azure Image Gallery](https://docs.microsoft.com/en-us/azure/virtual-machines/shared-image-galleries) (now the Azure Compute Gallery) but this doesn’t offer the flexibility or controls that I want specifically for Citrix deployments currently.

As a principal, I always use Azure snapshots to update Machine Catalogs. I have a small script [CreateOSSnapshot](https://github.com/JamesKindon/Citrix/blob/master/Azure/CreateOSSnapshot.ps1) here which I used to ensure consistent naming.

## Filling the Gap

To fill the gap, I have written a [script](https://github.com/JamesKindon/Azure/blob/master/ReplicateAzureSnapshot.ps1) in PowerShell designed to (but not limited to) be executed via [Azure Automation](https://docs.microsoft.com/en-us/azure/automation/overview) Accounts which offers the following capabilities/modes:

-  `DifferentSubDifferentRegion` will replicate Snapshots from one region to another region in a different subscription. This is the ***default*** mode of operations
-  `DifferentSubSameRegion` will replicate Snapshots from one region to another region in the same subscription
-  `SameSubDifferentRegion` will replicate Snapshots to the same region in different subscription

The script has the following modes of operation and filtering controls:

-  `Tag Filtering`. Only replicate snapshots with a specific tag (***Tag***: `SnapReplicate` ***Value***: `Replicate`)
-  `Sync`. This mode will compare the source and target Resource Groups to keep them in sync. If you delete a snapshot in the source, it will be removed from the target. The source Resource Group is **always** authoritative

To implement the solution, you simply need an Azure Automation Account using a [System Assigned Managed Identity](https://jkindon.com/migrate-azure-runbook-runas-to-system-assigned-managed-identity/).

The managed identity must have ***contributor*** permissions on the **source** *and* **target** Resource Groups as the script will handle locks on existing Snapshots, Storage Account creation and deletion when going cross-region and creation/deletion of snapshots in the target Resource Group when in Sync mode
{:.attention}

Update the Azure Modules on the runbook to make sure they are the latest (PowerShell 7.1 please)

[![ModuleUpdate]({{site.baseurl}}/assets/img/microsoft-azure-snapshot-replication-framework/ModuleUpdate.png)]({{site.baseurl}}/assets/img/microsoft-azure-snapshot-replication-framework/ModuleUpdate.png)

Add a new runbook with PowerShell 7.1

[![NewRunbook]({{site.baseurl}}/assets/img/microsoft-azure-snapshot-replication-framework/NewRunbook.png)]({{site.baseurl}}/assets/img/microsoft-azure-snapshot-replication-framework/NewRunbook.png)

Set your logging levels as below:

[![Logging]({{site.baseurl}}/assets/img/microsoft-azure-snapshot-replication-framework/Logging.png)]({{site.baseurl}}/assets/img/microsoft-azure-snapshot-replication-framework/Logging.png)

Make sure you have permissions on both the **source** and **target** Resource Groups as above

[![Permissions]({{site.baseurl}}/assets/img/microsoft-azure-snapshot-replication-framework/Permissions.png)]({{site.baseurl}}/assets/img/microsoft-azure-snapshot-replication-framework/Permissions.png)

Add the [code](https://github.com/JamesKindon/Azure/blob/master/ReplicateAzureSnapshot.ps1), and set your variables either statically within the script, or within the schedule if you are doing multiple jobs and sync cycles to different regions. Either is fine.

-  `SourceSubscriptionID` The source subscription ID of where your snapshots live
-  `TargetSubscriptionID` If moving cross subscription, this is the target Subscription ID for where your snapshots will sync to
-  `SourceResourceGroup` The source Resource Group (name) of where your snapshots live
-  `TargetResourceGroup` The target Resource Group (name) for where your snapshots will sync to
-  `TargetRegion` If moving region, the target region for your snapshots
-  `SnapshotName` Individual snapshot name to sync. Cannot be used with `Sync` or `UseTagFiltering` Params
-  `Mode` Offers 3 models of operation `DifferentSubDifferentRegion` (Default), `DifferentSubSameRegion` and `SameSubDifferentRegion`
-  `Sync` Sets the flag to compare source and destination Resource Group Snapshots. **This is a sync job. If a deletion occurs in the source, it will be mirrored in the target**. Values: `Sync`, `DontSync`
-  `UseTagFiltering` The recommended model for `Sync`. Requires setting a tag on snapshots in the source which are targeted for sync to the target. Ignores all other snaps
-  `isAzureRunbook` The designed operational model for this runbook (Defaults to `true`)
-  `LogPath` Logpath output for all operations – valid when not running as a runbook primarily
-  `LogRollover` Number of days before logfiles are rolled over. Default is `5`

Assign a schedule and off you go. All output will be pushed to the console so you can review it appropriately.

[![SnapReplicate]({{site.baseurl}}/assets/img/microsoft-azure-snapshot-replication-framework/SnapReplicate.png)]({{site.baseurl}}/assets/img/microsoft-azure-snapshot-replication-framework/SnapReplicate.png)

That's it. Now you have a zero-touch synchronisation solution which ensures local copies of your snapshots ready for quick and efficient catalog creations. Easy.

## Download

You can download the [ReplicateAzureSnapshot script here](https://github.com/JamesKindon/Azure/blob/master/ReplicateAzureSnapshot.ps1). Feel free to consume and adapt as you see fit.

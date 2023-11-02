---
layout: post
title: "Shrinking an Azure OS Disk to enable Ephemeral capability"
permalink: "/shrink-azure-os-disk-for-ephemeral/"
categories: [Citrix, CVAD, Cloud, Azure, Ephemeral]
description: Shrink that OS Disk to enable hyper fast Ephemeral goodness
image:
  path: /assets/img/shrink-azure-os-disk-for-ephemeral/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro to Ephemeral Disks

Microsoft Azure offers the concept of Ephemeral Disks, a nice way of having hyper fast OS disk performance, at no cost. I have [written previously about Citrix MCS and Azure Ephemeral Disks](https://jkindon.com/citrix-mcs-and-azure-ephemeral-disks/) and why you should use them.

[Ephemeral Disks](https://docs.microsoft.com/en-us/azure/virtual-machines/ephemeral-os-disks) have to slot into either the provided Cache, or the Temp SSD associated with an instance size in Azure. This means that sizing your VM OS disk becomes critical. A 127 GiB OS disk, doesnt go into a 100 GiB Cache. Square peg. Round hole.

Unfortunately, shrinking a managed disk in Azure is not the simplist of tasks, so if you have deployed your Win10 virtual machines on the default 127 GiB P10 OS disk, you may be scratching your head at the instance size required to support your Ephemeral Disk requirements. This is likely a challenge for single session OS deployments rather than multi, where you are typically using larger instance sizes.

For AVD, our friends at Nerdio have a [script action](https://github.com/Get-Nerdio/NMW/blob/main/scripted-actions/azure-runbooks/Shrink%20OS%20Disk.ps1) that can assist in shrinking the OS disk. [Marcel Meurer](https://twitter.com/marcelmeurer) also provides capability via both [Hydra](https://github.com/MarcelMeurer/WVD-Hydra) and [WVD Admin](https://blog.itprocloud.de/Windows-Virtual-Desktop-Admin/) (the latter of which you could actually use to action what I am doing below to be fair). The Nerdio action is based on the same blog that I looked at, with revamped code to suit Nerdios requirements (nice job - I nabbed your guest re-partitioning logic).

## The Script

Luckily, for those without access to those tools, there is PowerShell. I have taken the [source code from here](https://github.com/jrudlin/Azure/blob/master/General/Shrink-AzDisk.ps1) which is [discussed here](https://jrudlin.github.io/2019-08-27-shrink-azure-vm-osdisk/), added a stack of code optimization, logic addition, error handling, full logging and selective cleanup capability, allowing a seamless, and safe way to take an oversize disk, and shrink it to the desired state.

My script will NOT delete your source OS disk, by design. That's on you to handle once you are happy. Everything else is cleaned up. My script will however:

-  Handle in guest partitioning via Azure Invoke-AzVMRunCommand (Thanks Nerdio)
-  Snapshot the source OS disk
-  Handle VM power state and attain lock on OS Disk
-  Create a temporary storage account and blob container
-  Upload the current OS disk to the container as a VHD
-  Create a temp managed disk and attach to the VM to handle footer info
-  Create a new managed disk from the uploaded source disk at the defined size, and set footer details
-  Switch the OS disk on the target VM, and validate boot is OK
-  Cleanup storage accounts and containers

For those (like me) that still want optimal performance on their image VM's, rather than the standard P6, you can always choose a higher performance tier for the smaller disk.

## Wrap-Up

Enjoy, and happy Ephemeral-Disking. [Code can be found here](https://github.com/JamesKindon/Azure/blob/master/ShrinkAzureOSDisk.ps1)
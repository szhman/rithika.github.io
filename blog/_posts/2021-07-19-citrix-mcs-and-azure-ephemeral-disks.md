---
layout: post
title: "Citrix MCS and Azure Ephemeral Disks"
permalink: "/citrix-mcs-and-azure-ephemeral-disks/"
categories: [Citrix, CVAD, Azure, Windows, MCS, Ephemeral Disks, Provisioning]
redirect_from: 
    - /2021/07/19/citrix-mcs-and-azure-ephemeral-disks
    - /2021/07/19/citrix-mcs-and-azure-ephemeral-disks/
description: Baseline testing Citrix MCS and Azure Ephemeral Disks
image:
  path: /assets/img/citrix-mcs-and-azure-ephemeral-disks/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

I wrote previously about [Citrix MCS and Shared Image Galleries](https://jkindon.com/2021/07/11/citrix-mcs-and-azure-shared-image-gallery/), with a focus being on the enablement of Ephemeral Disks in Microsoft Azure. Ephemeral disks are a massive addition to the MCS toolkit, potentially having some major impacts on Azure consumption and performance.

## What is an Ephemeral Disk

For the most part, most machines in Microsoft Azure have the following components:

-  An Operating System Disk (typically a managed disk on remote storage)
-  A Temporary Disk which is flushed on reboot. Azure by default will place the page file on this disk for instances built in Azure
-  A machine "Cache" which lives locally on the hypervisor running the VM

Not all instance types are the same. Not all instance types have a temporary disk, and not all instance types have a cache.

Take an NV12 spec machine, for example, it has a massive temporary disk (300Gb), however, it has no cache. A Dsv4-series VM has neither.

An Ephemeral disk is simply put, your Operating System disk placed onto either the VM Cache `If` the size of that Cache is larger than the OS disk, or, at time of writing, [a preview feature allows the OS disk to be housed on the Temporary disk](https://docs.microsoft.com/en-us/azure/virtual-machines/ephemeral-os-disks#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks) `If` the Temp disk is larger than the OS disk.

**Update 15.12.2021** - this feature is now GA and production ready
{:.attention}

Why do we care? Simple. An Ephemeral Disk is free and its fast. There is no charge for operating an Ephemeral Disk. It is housed on high performing, low latent local SSD disk.

Ephemeral Disks do NOT survive a VM de-allocation. They are destroyed and recreated, they do, however, survive an operating system reboot. This makes them a perfect candidate for non-persistent VDI or SBC based workloads, a Citrix MCS specialty...

## Citrix MCS and Azure Ephemeral Disks

Citrix MCS now can utilise cached base ephemeral disks. Once Microsoft removes the preview status of "cache disk based Ephemeral Disks", MCS will support that too with a logic of "*if the cache is big enough – use it, if it's not, use the temp disk*".

**Update 15.12.2021** - Citrix MCS will support deployment of the OS disk to either the Cache Disk if present and capable, or to the Temp SSD if the Cache is not. A preference to local cache is always the default, if the machine is resized and the temp SSD disk is the only available option, MCS will handle the configuration accordingly
{:.attention}

The Citrix iteration of this relies on the use of a shared image gallery, and as of the time of writing, requires a custom provisioning scheme driven by PowerShell. I am expecting at some point soon, this will be a tick box in Studio.

**Update 18.10.2021** - you can now select to consume Azure Ephemeral Disks via the CVAD Studio GUI
{:.attention}

In a Microsoft world, the use of Ephemeral Disks is typically associated with services that can consume scale sets, and one of the significant changes is that if an Ephemeral Disk is in use, the page lives on the OS disk. With MCS however, the brains of provisioning still move this page file to the Temp Disk, freeing up IOPS for more important use cases.

### VM Size Considerations

The first thing you want to be confirming is that your [instance series and size supports Ephemeral Disks](https://docs.microsoft.com/en-us/azure/virtual-machines/dv3-dsv3-series).

[![Supported Dsv3 Spec Machines (Ephemeral OS Disk)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_Supported.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_Supported.png)

The size of your virtual machine will directly impact the size of both your available Temp SSD and VM Cache. For machines deployed in Azure, you are typically going to be working on a 127GiB OS Disk, which equates to a P10 Premium Disk.

This means that for an Ephemeral disk to be realistic, you need a VM that has a Cache **larger** than the 127GiB OS Disk. If I play in the Dsv3-series, this means a minimum of a Standard_D8s_v3 Instance type. This instance has:

-  A 64GiB Temp SSD Disk
-  A 200GiB Cache, more than OK to slot in our 127 GiB OS Disk

[![Standard_D8s_v3 sizing]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_Size.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_Size.png)

If I were to play in the DSv2-series, a minimum instance type of Standard_DS3_v2 would be required as it has a 172 GiB cache.

[![Standard_DS3_v2 sizing]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_Size2.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_Size2.png)

Not all deployments will build their images natively in Azure. Some customers will build an image on-prem and upload it to Azure, resulting in potentially a smaller OS disk size, which results in a smaller managed disk, resulting in smaller IOPS capability. Ephemeral Disks may well offer superior performance at a reduced cost, however, there may be an uplift in instance sizes to support a cache big enough to house the disk. Each environment is going to be unique.

Our friends at NERDIO have a nice write up [comparing standard SSD to Ephemeral Disks](https://getnerdio.com/academy/how-to-speed-up-windows-virtual-desktop-with-ephemeral-os-disks-and-saving-on-storage-costs-too/.),

I haven’t seen anything helping to compare premium SSD to Ephemeral so I pinged one of my good friends in the UK Mr [Leee Jeffries](https://twitter.com/leeejeffries?lang=en) who has forever been at the forefront [of performance baselining](https://www.leeejeffries.com/), and we did some basic comparisons with some very interesting results.

For VM’s that have both Cache Disk and Temp SSD availability, should an Ephemeral Disk be used, I would think it wise to still move, where possible, crud data to the Temp SSD (App-V cache, Cloud Cache for FSLogix, Page File etc)

Comparing the [throughput spec against the temp/cache storage of the VM against the premium disk capability](https://azure.microsoft.com/en-au/pricing/details/managed-disks/) is a good starting point.

### Consuming Ephemeral Disks with Citrix MCS

**Update 18.10.2021** - you can now select to consume Azure Ephemeral Disks via the CVAD Studio GUI
{:.attention}

[![GUI]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/GUI.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/GUI.png)

    Above: Updated ability to deploy Ephemeral Disks via the GUI

**Update 18.10.2021** Note that MCSIO is not available when using Ephemeral Disks. The GUI will remove the option for any disk cache configurations
{:.attention}

**NOTE:** You no longer are forced to deploy via PowerShell, however it is still a valid option
{:.warning}

You will need to deploy a Citrix Catalog with a custom provisioning scheme via PowerShell. This Catalog will also need to be deployed with a Shared Image Gallery. These two go hand in hand. This is far less scary than it sounds, however for those not comfortable in PowerShell, will likely be a small challenge. The three key components are simply:

-  We must specify _UseManagedDisks_
-  We must specify _UseSharedImageGallery_
-  We must specify _UseEphemeralOsDisk_

[![Custom ProvScheme including Ephemeral Disks]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/CustomProvScheme.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/CustomProvScheme.png)

When your machines are booted, all things being well, you will find that your VM will show that your Ephemeral OS disk stores the OS Cache.

[![Ephemeral disk enabled and active]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_DiskEnabled.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_DiskEnabled.png)

Your Resource Group will no longer show an OS disk object

[![No more OS disk]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_ResourceGroup.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_ResourceGroup.png)

You will also find that your OS is represented as a standard HDD LRS disk (this is normal)

[![Ephemeral disks display as Std HDD]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_OSDisk_StdHDD.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/Eph_OSDisk_StdHDD.png)

## Performance Baselining with fio

Leee and I worked through a couple of scenarios looking to understand what a Premium P10 Disk played like, in comparison to an Ephemeral Disk. Below is our environment

-  Standard D8s v3 spec instance
-  Windows 10 Enterprise for Virtual Desktops, Image built by packer
-  Citrix Machine Creation Services deployment of two machines
    -  A catalog using a SIG deployment model with Premium SSD Disks (P10)
    -  A catalog using a SIG deployment with Ephemeral Disks

I read a [fantastic article](https://arstechnica.com/gadgets/2020/02/how-fast-are-your-disks-find-out-the-open-source-way-with-fio/) which helped immensely in driving some basics with the open source fio tool. You can pull down fio from [https://bsdio.com/fio/](https://bsdio.com/fio/)

I ran three baseline tests on both VM’s

-  Single 4KiB random write process
-  16 parallel 64KiB random write processes
-  Single 1MiB random write process (I ran this twice as the results were kind of eye opening)

Check out the results below, the ephemeral disk destroys the P10 in all tests, those results are very eye opening as to the capability of that local Cache storage

#### Single 4KiB random write process

-  `P10`: WRITE: **bw=42.8MiB/s (44.8MB/s)**, 42.8MiB/s-42.8MiB/s (44.8MB/s-44.8MB/s), **io=4186MiB (4390MB)**, run=97875-97875msec
-  `EPH`: WRITE: **bw=57.7MiB/s (60.5MB/s)**, 57.7MiB/s-57.7MiB/s (60.5MB/s-60.5MB/s), **io=5544MiB (5814MB)**, run=96081-96081msec

#### 16 parallel 64KiB random write processes

-  `P10`: WRITE: **bw=2795MiB/s (2931MB/s)**, 81.1MiB/s-528MiB/s (85.0MB/s-554MB/s), **io=243GiB (261GB)**, run=88748-88999msec
-  `EPH`: WRITE: **bw=4003MiB/s (4197MB/s)**, 211MiB/s-356MiB/s (221MB/s-373MB/s), **io=275GiB (295GB)**, run=69430-70220msec

#### Single 1MiB random write process (run 1)

-  `P10`: WRITE: **bw=24.3KiB/s (24.9kB/s)**, 24.3KiB/s-24.3KiB/s (24.9kB/s-24.9kB/s), **io=2048KiB (2097kB)**, run=84271-84271msec
-  `EPH`: WRITE: **bw=383MiB/s** (402MB/s), 383MiB/s-383MiB/s (402MB/s-402MB/s), **io=36.3GiB (39.0GB)**, run=97012-97012msec

#### Single 1MiB random write process (run 2)

-  `P10`: WRITE: **bw=25.4KiB/s** (26.0kB/s), 25.4KiB/s-25.4KiB/s (26.0kB/s-26.0kB/s), **io=2048KiB (2097kB)**, run=80758-80758msec
-  `EPH`: WRITE: **bw=1315MiB/s** (1379MB/s), 1315MiB/s-1315MiB/s (1379MB/s-1379MB/s), **io=125GiB (135GB)**, run=97570-97570msec

## Performance Baselining with IOMeter

Leee provided some nice guidance on IOmeter based on some information that Jim Moyle (who is surprised here) wrote a while back for baseline VDI workloads. We took those tests and threw them against the same environment.

Below is the VDI spec for Iometer:

[![Iometer VDI spec as per Jim Moyle]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_vdispec.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_vdispec.png)

And below is the spec we used to throw against the machine (8 workers)

[![8 node worker specification]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_workerspec.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_workerspec.png)

Now here is where things get fun. I ran this spec against both machines, AAE-VM-SIG01 uses a 127 GiB Premium P10 Disk and AAE-VM-ED01 is running with an Ephemeral Disk

[![8 worker configuration running against the P10 Disk]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_P10_Running.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_P10_Running.png)

[![8 worker configuration running against the Ephemeral Disk]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_Eph_Running.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_Eph_Running.png)

The results are obvious, the Ephemeral once again punished the P10\. Below is a breakdown of the IOPS across both disks

[![P10 IOPS per second]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_P10_IOPS.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_P10_IOPS.png)

[![Ephemeral IOPS per second]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_Eph_IOPS.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_Eph_IOPS.png)

Below is a breakdown of the response times across both disks

[![P10 response time]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_P10_Response.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_P10_Response.png)

[![Ephemeral response time]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_Eph_Response.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-ephemeral-disks/iometer_Eph_Response.png)

A summary of results is outlined below:

| **Iometer display** | **P10** | **Ephemeral** |
| :-- | --- | --- |
| Total I/O per Second | 3064.59 | 15977.28 |
| Total MBs per Second (Decimal) | 12.55 MBPS (11.97 MiBPS) | 65.44 MBPS (62.41 MiBPS) |
| Average I/O Response Time (ms) | 83.5275 | 16.0204 |
| Maximum I/O Response Time (ms) | 2154.5712 | 377.5321 |
| % CPU Utilization (total) | 16.54% | 25.01% |

## Summary and Final Thoughts

It's not rocket science here, the results based on baselining tools speak for themselves. The Ephemeral disk annihilates the P10 disk. From a user experience standpoint, the general "feel" was that both disks performed fairly similarly until they were under load. Under the same load, the premium disk-based instance was barely useable, however, the Ephemeral based instance was still happily responsive. Of course, a P-series disk can have a high performance tier associated with it as required, and the results will change, but for a standard P10 vs Ephemeral deployment, the options are interesting.

This is good news for those that use multi-session environments as well as those suffering due to small disks in Azure and the associated price point the P-Series disk uplifts. Whilst it may not be the right answer in all use cases, this is yet another awesome piece of integration with MCS and Azure and adds another arrow to the quiver for those looking to improve performance and user experience in the Azure cloud.

---
layout: post
title: "Migrate XenServer VMs to HyperV"
permalink: "/migrate-xenserver-vms-to-hyperv/"
description: "Getting out of Citrix XenServer and over to Microsoft HyperV"
categories: [Conversion, HyperV, Windows, XenServer]
redirect_from: 
    - /2019/01/06/migrate-xenserver-vms-to-hyperv
    - /2019/01/06/migrate-xenserver-vms-to-hyperv/
image:
  path: /assets/img/migrate-xenserver-vms-to-hyperv/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

I recently had the unenviable task of moving a few XenServer based Virtual machines across to Hyper-V. Not the simplest of all tasks and disturbingly complex compared to the simplicity of moving VM's in and out of vSphere. The tools are limited, and whilst there are some freebie options around to help, it’s the process rather than the tooling that is the challenge. Below is what (after many attempts and head meets wall moments) I found to be the best way of consistently moving the workload from XenServer 7.6 to Hyper-V 2016. The VM's in question are Windows Server 2016

## Step 1. Uninstall XenServer Tools/Management Agent

Snapshot your server.

These suckers go deep on the VM. I needed to remove these on the source VM to start with which is a commonly known requirement in the XenServer world. I came across some odd behaviours where even when removing the agent from add/remove programs, the driver sets would stay in play (confirmed under device management). To resolve, I had to reinstall the latest management agent, and then uninstall. This then left me with plenty of ghost devices to deal with.

[![Operational Management Tools]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/ManagementToolsOperational.png)]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/ManagementToolsOperational.png)

Operational Management Tools
{:.figcaption}

[![Uninstall Management Agents]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/UninstallManagementTools.png)]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/UninstallManagementTools.png)

Uninstall Management Agents
{:.figcaption}

I allowed the reboot despite some posts online saying to ignore it

[![No more tools]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/ManagementToolsGone.png)]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/ManagementToolsGone.png)

No more tools
{:.figcaption}

## Step 2. Remove Ghost Devices

Anyone who has dealt with virtualisation and migrations of workloads will understand the concept of Ghost devices. Unfortunately removing the XenServer Management Agent does nothing to clean up its drivers or ghost devices, so you will need to do this manually. Luckily, Trentent Tye posted a nice PowerShell Script [here](https://theorypc.ca/2017/06/28/remove-ghost-devices-natively-with-powershell/) to make your life easier.

I would suggest checking device manager first and make sure that all XenServer based devices are 100% marked as Ghost (greyed out). Action the below via PowerShell:

```powershell
set devmgr_show_nonpresent_devices=1
start devmgmt.msc
```

[![Ghost Devices via Device Manager]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/DevMgmtGhosts.png)]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/DevMgmtGhosts.png)

Now run `RemoveGhosts.ps1` to clean out the Ghosts

[![RemoveGhosts]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/RemoveGhosts.png)]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/RemoveGhosts.png)

Note below is a large list of Ghosts that have been removed

[![Removed Ghost Devices]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/RemovedGhosts.png)]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/RemovedGhosts.png)

## Step 3. Remove drivers from source VM

There are a few drivers that are deployed into the `c:\windows\system32\drivers` folder on your virtual machine. I needed to remove these manually before doing any conversion work via PowerShell:

```powershell
Push-Location c:\windows\system32\drivers\
Get-ChildItem | ?{$_.Name -like "xen*"}
```

[![Delete the Driver File]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/RemoveDrivers.png)]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/RemoveDrivers.png)

Delete the drivers
{:.figcaption}

Reboot your system

## Step 4. Convert the VM

I chose to use Disk2Vhd from sysinternals for this part of the process, you can realistically do whatever you like tool wise, I like Disk2Vhd as its never ever failed me. Be warned, you will be using a stock standard legacy NIC without XenServer tools, so your transfer speeds will suck. You could alternatively choose to do a vm export via XenServer CLI at this point, but I didn’t test this (Thanks [Tobias Kreidl](http://@tkreidl) for the pointer)

`disk2vhd * c:\vhd\snapshot.vhd`

[![Disk2VHD in action]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/Disk2VHD.png)]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/Disk2VHD.png)

## Step 5. Check the VHDX

This part was pretty critical on a couple of my VM’s. After successfully converting the existing VM to a VHDX file, I mounted it to double check the drivers directory and make sure none of the Xen* drivers were still around, there were machines that managed to have these drivers in tact which would prevent the machines booting in Hyper-V.

You can also run defrags etc at this point, nice and easy whilst the VHDX is mounted.

## Step 6. Move on in life

It should now be a simple job of creating a new Hyper-V VM, pointing It to your converted VHDX file and powering on

[![Happy HyperV Machine]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/MachineBoot.png)]({{site.baseurl}}/assets/img/migrate-xenserver-vms-to-hyperv/MachineBoot.png)

Happy HyperV Machine
{:.figcaption}

## Gotchas

-  I had some issues where the machine would blue screen on boot once converted - sure enough, those drivers we deleted off the image prior to imaging were back, most probably due to a reboot somewhere in the mix which would inject them back in
-  It is slow to convert across the wire when you don’t have XenServer Tools enabled, if you are doing this in bulk, an export at the XenServer layer may be far more efficient. I had sub 10 machines so manual was fine
-  If your Windows Server has issues with activation, I had no success in converting. The machines had to be activated and healthy with no pending installations for the conversion to successfully take

## Summary

Hopefully this helps those that get stuck in migrating from XenServer to Hyper-V, as someone who has done extensive migration work across P2V and V2V over the years, this simple conversion task was one of the more frustrating ones with a few gotchas. Bring on vSphere converter I say

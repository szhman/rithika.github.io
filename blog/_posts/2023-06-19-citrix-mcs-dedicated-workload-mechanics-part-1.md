---
layout: post
title: "Citrix MCS Dedicated Workload Provisioning - ProvVM and BrokerMachine Mechanics"
permalink: "/citrix-mcs-dedicated-workload-mechanics-part-1/"
categories: [Citrix, MCS, Provisioning, PowerShell]
description: Delving into the mechanics of the MCS ProvVM relationship
image:
  path: /assets/img/citrix-mcs-dedicated-workload-mechanics-part-1/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Citrix MCS Intro

Citrix Machine Creation Services is often used to provision dedicated, persistent virtual desktop workloads. These machines, once provisioned, typically take on a life of their own and post provisioning, MCS currently plays a minimal role in their existence.

There are exceptions to this tradition, where certain new features are being made available to persistent machine workloads provisioned by MCS:

-  Reset OS disk, which allows the original OS disk to be reset back to its default provisioned state.
-  {*Another feature here which I will add once it has been released and I wont be in breach of NDA*}.

Persistent VMs deployed with MCS have a couple of challenges when looking at changing the underlying infrastructure:

-  They are tied to the Catalog they were provisioned in, and that catalog is tied to the hosting connection it was created against. You cannot change this and keep the MCS relationship in tact.
-  If something happens to the underlying infrastructure, where the ID of the machine changes at the hypervisor layer, then the machine is, for the most part, orphaned.

For these reasons, when dealing with scenarios such as disaster recovery, migration or infrastructure refreshes, it is typically recommened to remove the virtual machine from the MCS provisioning world, and bring the machine back in as a manual power managed instance. Once this is completed, there is a lot more flexibility around what can be done with the machine. I wrote a script a while back which can [automate the process of moving from an MCS provisioned catalog, to a manual power-managed catalog](https://github.com/JamesKindon/Citrix/blob/master/Migration%20Scripts/MigrateMCSToManual/MigrateMCSToManual.ps1).

## The Provisioning Puzzle

Let's take a look at what makes up the MCS provisioned machine in the backend, because there are some options that are quite interesting to delve into.

To start, there is the concept of a `ProvVM`. This is the record that MCS uses to manage the lifecycle of the machine it has created. It is a unique record that exists for both non-persistent and persistent virtual machines. It contains two key records of interest: `VMid` which is the identifier of the virtual instance itself and `IdentityDiskId` which is the unique identifier of the identity disk on the hypervisor.

Secondly, there is the `BrokerMachine`. The `BrokerMachine` is the actual representation of the virtual machine on the hypervisor. Every machine within Citrix, regardless of its provisioning type has a `BrokerMachine` record. For our persistent MCS scenario, this record holds a few of key components of interest: `HostedMachineId` which is the unique identifier of the virtual machine, `HypervisorConnectionUid` which is the identifier of the hosting connection which knows about that virtual machine and the `ProvisioningType`:`MCS` flag which tells the Broker that this is an MCS provisioned machine.

Lastly, the `IdentityDisk`. The Identity disk for the most part doesn't currently do a huge amount in persistent MCS VDI environments, however, there are quite a few enhancements on the way which will 100% rely on the Identity disk being in tact from an ongoing lifecycle management standpoint.

## Linking it Together in a Nutanix Hosting Scenario

Different hosting connections and provisioning platforms will have different ways of linking these components together, but in the Nutanix world, the following applies:

-  The Nutanix virtual machine has a unique `uuid`
-  The Nutanix virtual machine Identity Disk has a unique `uuid`

In a persistent MCS environment on Nutanix AHV, the Nutanix VM `uuid`, the `BrokerMachine`:`HostedMachineId` and the `ProvVM`:`VMId` are identical. This is what links everything together. Additionally, the `ProvVM`:`IdentityDiskId` is matched to the Nutanix `uuid` of the Identity Disk.

## Remediating The Gap

In some scenarios (disaster recovery is a great example), the `uuid` of the virtual machine can change, rendering the machine orphaned from an MCS standpoint. If we want to remediate the MCS relationship, there are a couple of options we can play with.

### Using the Citrix VAD PowerShell SDK

Using the native option within Citrix, from CVAD 2212 onwards, the `Set-BrokerMachine -HostedMachineId ""` does indeed update the `BrokerMachine`:`HostedMachineId` and the  `ProvVM`:`VMId` but it does not update the `ProvVM`:`IdentityDiskID`.

Conversely, you can use the `Remove-BrokerMachine` PowerShell command to remove the `BrokerMachine` record from the Citrix Database for an MCS provisioned machine. This does not delete the `ProvVM` record. If the `ProvVM` object is still in the database, and the `HostedMachineId` / `uuid` of the VM hasn't changed, you can simply use `New-BrokerMachine -HostedMachineId "" -HypervisorConnectionUid ""` cmdlet to bring the machine back into the MCS world.

The current release documentation as at 18.06.2023 reads as:

> `HostedMachineId`: The unique ID by which the hypervisor recognizes the machine. **This may only be set for VMs which are not provisioned by MCS.**

> `HypervisorConnectionUid`: The hypervisor connection that runs the machine. **This may only be set for VMs which are not provisioned by MCS.**

The documentation from Citrix is being updated to reflect things accurately. I assure you, technically it works just fine. This means that you can remediate a machine which has had its `uuid` changed, allowing the MCS relationship to stay in tact.

### Updating the SQL Database

The challenge with PowerShell SDK is twofold:

1.  It is limited to `2112` onwards so has a smaller scope of supported deployments.
2.  It does not update the `IdentityDiskID`.

So the second option, is to alter the database directly and update the `ProvVM` object. You can use standard SQL tools, or if you are a PowerShell fan, then `Invoke-SQLCmd` is your friend against the Citrix Site Database. The `ProvVM` records are stored in the `ProvisionedVirtualMachine` table.

You can use a combination of `Remove-BrokerMachine` and `New-BrokerMachine` to remove the broker machine records, and if the `ProvVM` SQL record is up to date with the new `uuid` of the VM, then MCS is a happy camper, and machines continue on in a happy place.

*Should* you do this? That one I leave up to you. *Can* you do this? Absolutely.

## Summary

Whilst a short post, hopefully this shines some insight into how persistent virtual machine relationships are handled when provisioned with MCS, and provides some options should your underlying virtual machine `uuid` change.

Would be very nice if we could have a `Set-ProvVM` PowerShell cmdlet Citrix...hint hint :)
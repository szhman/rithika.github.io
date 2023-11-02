---
layout: post
title: The Evolution of Citrix Machine Creation Services with Microsoft Azure
permalink: "/the-evolution-of-citrix-machine-creation-services-with-microsoft-azure/"
categories: [Citrix, UPM, Profiles, Windows, CVAD, Cloud, Azure, evolution]
redirect_from: 
    - /2021/07/08/the-evolution-of-citrix-machine-creation-services-with-microsoft-azure
    - /2021/07/08/the-evolution-of-citrix-machine-creation-services-with-microsoft-azure/
    - /2021/07/08/the-evolution-of-citrix-machine-creation-services-with-microsoft-azure/AMP
    - /2021/07/08/the-evolution-of-citrix-machine-creation-services-with-microsoft-azure/AMP/
description: Tracking the progress of Citrix MCS and Microsoft Azure
image:
  path: /assets/img/the-evolution-of-citrix-machine-creation-services-with-microsoft-azure/post_default_image.jpg
sitemap: true
hide_last_modified: false
comments: true
related_posts:
  - evolution/_posts/2023-09-20-the-evolution-of-citrix-profile-management.md
  - evolution/_posts/2023-09-20-the-evolution-of-citrix-wem-service.md
  - evolution/_posts/2023-09-20-the-evolution-of-citrix-wem.md
---

<!--excerpt-->

-  Table of Contents
{:toc .large-only}

There is a lot of constant improvement being executed by the MCS team at Citrix, the release cadence is impressive and the feature enhancements significant. I spend a lot of time in Microsoft Azure with Citrix Cloud with a lot of happy customers. I thought it would be worthwhile keeping a rolling tally of new features with MCS and how it relates to Azure, so that we don't lose sight of how much value add is provided.

I will do my best to maintain this list as and when features come out, as well as some commentary around their value where I can.

It is important to be across the options when designing your delivery platform on Azure, many changes have a direct implication on the ongoing operational costs associated with running workloads on/in Azure, as well as availability and global deployment options. Looking at what we have now, vs what was available 12 months ago, many designs and deployments would look remarkably different.

## September 2023

### [Support for creating empty machine catalogs](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create#machines)

In Full Configuration, you can now create a machine catalog without immediate VM creation. With this feature, you can postpone VM creation until back-end hosts are fully prepared or VM provisioning is completed, gaining more flexibility in creating catalogs. Currently, this feature applies only to Machine Creation Services-provisioned catalogs.

### [Removed the Azure Germany option](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#new-and-enhanced-features)

In line with the closure of Microsoft Cloud Deutschland on October 29, 2021, Citrix removed the Azure Germany option from the host connection creation page.

## August 2023

### [VDA upgrade support for Azure Quick Deploy-created machine catalogs](https://docs.citrix.com/en-us/citrix-daas/upgrade-migrate#upgrade-vdas-using-the-full-configuration-interface)

With Full Configuration, you can now enable **VDA Upgrade** for machine catalogs created through Azure Quick Deploy and then perform **Upgrade VDA** on them for immediate or scheduled upgrades.

### [Update properties of individual VMs](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage/manage-machine-catalog-azure#update-properties-of-individual-vms)

An Azure only feature right now, you can now update properties of individual VMs in a persistent MCS machine catalog using a PowerShell command. This implementation helps you to manage individual VMs efficiently without updating the entire machine catalog.

### [Restrict upload and download of managed disks](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#august-2023)

As per Azure policy, you cannot upload or download more than five disks or snapshots at the same time with the same disk access object. With this feature, the limit of five concurrent upload or download is not enforced if you:

-  Configure `ProxyHypervisorTrafficThroughConnector` in `CustomProperties`, and
-  Do not configure Azure policy to create Disk Accesses automatically for each new disk to use private endpoints.

### [Support for assigning a specific drive letter to MCS I/O write-back cache disk](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create#assign-a-specific-drive-letter-to-mcs-io-write-back-cache-disk)

Previously, the Windows operating system automatically assigned a drive letter to MCS I/O write-back cache disk. You can now assign a specific drive letter to MCS I/O write-back cache disk. This implementation helps to avoid conflicts between the drive letter of any applications that you use and the drive letter of MCS I/O write-back cache disk. This feature is applicable to only Windows operating system.

Not applicable when Azure temporary disk is used as write-back cache disk
{:.attention}

Applicable drive letter for write-back cache disk: `E` to `Z`
{:.attention}

### [Retry creating catalog after failure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage#retry-catalog-creation)

When catalog creation fails, you can now retry the creation job. On failure, you can review troubleshooting information to help resolve the issues. The information describes the issues found and provides recommendations for resolving them. Failed catalogs are marked with an error icon. To see the details, go to the **Troubleshoot** tab of each catalog.

### [Enable cleanup of stale Azure AD joined devices](https://docs.citrix.com/en-us/citrix-daas/install-configure/connections/connection-azure-resource-manager#enable-azure-ad-joined-device-management)

This one scrapes in on the Azure front, but only just. Citrix introduced an option to simplify the cleanup of stale Azure AD joined devices in Citrix DaaS. Previously, you had to run a custom PowerShell script to perform the task. Enabling this option grants host connections permission to automatically clean stale Azure AD joined devices.

### [Monitor image update status for catalogs using Full Configuration](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#august-2023)

You can now monitor image update statuses for non-persistent machine catalogs using a new column, **Image Update**. This column indicates whether images of a catalog are **Fully updated**, **Partially updated**, or **Pending update**.

To show the column in the Machine Catalogs table, follow these steps:

-  In the **Machine Catalogs** node, select the **Columns to Display** icon in the action bar.
-  Select **Machine Catalog** -> **Image Status**.
-  Click **Save**.

Displaying the Image update column might degrade the console performance. We recommend displaying it only when necessary.

### [Image refresh option for Catalog Tasks](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#new-and-enhanced-features-2)

When selecting master images for machine catalogs, you can now quickly get the most up-to-date master image list using the Refresh option at the top right. Additionally, a **Refresh** option is available for machine profiles and host groups in Azure catalogs.

## July 2023

### [Support for getting a list of orphaned resources on Azure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage.html#retrieve-a-list-of-orphaned-resources)

In Azure environments, you can now get a list of orphaned resources that are created by MCS but are no longer used by MCS. This feature helps to avoid extra costs.

### [Support for creating persistent multi-session machines using Full Configuration](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create.html#desktop-types-desktop-experience)

When creating a catalog of multi-session machines, you can now specify whether to make them persistent. For persistent multi-session machines, keep in mind that changes users make to the desktops are saved and accessible to all authorized users.

### [Support for deleting Azure AD devices](https://docs.citrix.com/en-us/citrix-daas/install-configure/create-machine-identities-joined-catalogs/create-azure-ad-joined-catalogs.html)

With this feature, stale Azure AD devices can be consistently deleted by assigning the Cloud Device Administrator role to the service principal and modifying the custom property of the hosting connection. If you do not delete the Azure stale AD devices, then the corresponding non-persistent VM stays in the initializing state until you manually remove it from the Azure AD portal.

### [Image refresh option](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#july-2023)

When selecting master images for machine catalogs, you can now quickly get the most up-to-date master image list using the Refresh option at the top right. Additionally, a Refresh option is available for machine profiles and host groups in Azure catalogs.

## June 2023

### [Ability to get historical errors and warnings associated with an MCS machine catalog](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage.html#retrieve-warnings-and-errors-associated-with-a-catalog)

Previously, you only got the latest warnings and errors associated with a machine catalog. With this feature, you can now get a list of the historical warnings and errors of an MCS machine catalog. This list helps you to understand any issues with your MCS machine catalog and fix those issues.

### [Full Configuration now preconfigures certain settings for Azure machines based on machine profiles](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#june-2023)

When you provision Azure VMs, Full Configuration now preconfigures the following settings based on the selected machine profile:

-  Host group
-  Disk Encryption Set
-  Availability Zone
-  License Type

### [Secure environment for Azure managed traffic](https://docs.citrix.com/en-us/citrix-daas/install-configure/connections/connection-azure-resource-manager.html#create-a-secure-environment-for-azure-managed-traffic)

Traditionally, customers relied on the public internet to let Azure endpoints interact with resources in the environment. As a result, security concerns were raised because the public internet was accessed. With this feature, MCS enables network traffic to be routed through Citrix Cloud Connectors in the environment. This makes the environment safer because  all Azure managed traffic originates from the customers environment. To enable this, add `ProxyHypervisorTrafficThroughConnector` in `CustomProperties`.

After you set the custom properties, you can configure Azure policies to have private disk access to Azure managed disks.

### [Support for provisioning catalog VMs with Azure Monitor Agent](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#provision-catalog-vms-with-azure-monitor-agent-installed)

Azure Monitor Agent (AMA) collects monitoring data and delivers it to Azure Monitor. With this feature, you can provision MCS machine catalog VMs (persistent and non-persistent) with AMA installed as an extension. This implementation enables monitoring by uniquely identifying the VMs in monitoring data.

Currently, MCS supports only the machine profile workflow for this feature.

## May 2023

### [Support for converting a non-machine profile-based machine catalog to machine profile-based machine catalog in Azure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage/manage-machine-catalog-azure.html#convert-a-non-machine-profile-based-machine-catalog-to-machine-profile-based-machine-catalog)

In Azure, you can now use a VM or template spec as a machine profile input to convert a non-machine profile-based machine catalog to machine profile-based machine catalog. Existing VMs and new VMs added to the catalog take property values from the machine profile unless overwritten by explicit custom properties.

### [Support for double encryption on managed disk in Azure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#double-encryption-on-managed-disk)

 In Azure, you can now create an MCS machine catalog with double encryption. Double encryption is platform-side encryption (default) and customer-managed encryption (CMEK). If you are a high security sensitive customer who is concerned about the risk associated with any encryption algorithm, implementation, or a compromised key, you can opt for this double encryption. Persistent OS and data disks, snapshots, and images are all encrypted at rest with double encryption.

### [Ability to reset the OS disk of a persistent VM in an MCS created machine catalog in Azure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage.html#reset-os-disk)

You can now use the PowerShell command `Reset-ProvVMDisk` to reset the OS disk of a **persistent** VM in an MCS created machine catalog. The feature automates the process of resetting the OS disk. For example, it helps in resetting the VM to its initial status of a persistent development desktop catalog created using MCS. Think of this as a reset back to the initial state of provisioning.

### [Improved host connection creation experience](https://docs.citrix.com/en-us/citrix-daas/install-configure/connections.html#step-1-connection)

Again, not an MCS specific function, but impacts the process of settings up an environment for MCS. You can now get the following information while you create a host connection:

-  List of all Citrix supported hypervisor plug-ins, including third party plugins
-  Availability of hypervisor plug-in. If the availability status is false, possible reason might be that Cloud Connector is not installed

This feature helps you correctly set-up a resource location and thus, create a host connection.

### [New option to turn off forced user logoff for Autocale](https://docs.citrix.com/en-us/citrix-daas/manage-deployment/autoscale/force-user-logoff.html)

Not quite MCS, but included as Autoscale and MCS provisioned workloads are so heavily entwined. A new option, **Neither notify nor force user logoff**, is now available on the **Manage Autoscale -> User Logoff Notification** page. With the option selected, Autoscale will neither force users to log off from machines in drain state nor notify users to log off and log on to a different machine.

### [There are several enhancements to Azure Active Directory security group capability](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#may-2023)

The following capabilities have been added:

-  [Ability to display the Azure AD assigned security groups for VMs to join](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#create-a-machine-catalog-using-an-azure-resource-manager-image) In Full Configuration, when you create Azure Active Directory joined VMs, an option, **Join an assigned security group as a member**, lets you add the Azure AD security group where the VMs reside to an assigned security group
-  [Support for renaming Azure AD security groups for VMs](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#create-a-machine-catalog-using-an-azure-resource-manager-image) For VMs added to an Azure AD security group through Citrix DaaS, you can now rename the security group using **Full Configuration -> Edit Machine Catalog**. Renaming occurs after you save the change. Names of Azure AD security groups must not contain the following characters: `@ " \ / ; : # . * ? = < > | [ ] ( ) '`.

### [Support for changing networks for connections](https://docs.citrix.com/en-us/citrix-daas/install-configure/connections.html#edit-networks)

In Full Configuration, you can now change networks for a connection. You can't unassociate networks from a connection if they are in use.

### [Ability to remove tags in Azure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage/manage-machine-catalog-azure.html#remove-tags)

Previously, `Remove-ProvVM` and `Remove-ProvScheme` PowerShell commands with `ForgetVM` parameter removed the VMs and machine catalogs from the Citrix database. However, the commands didn't remove the tags from the resources. You had to individually manage the VMs and machine catalogs that weren’t deleted entirely from all the resources. With this feature, you can use:

-  `Remove-ProvVM` with `ForgetVM` parameter to remove VMs and tags created on the resources from a single VM or a list of VMs from a machine catalog.
-  `Remove-ProvScheme` with `ForgetVM` parameter to remove a machine catalog from the Citrix database and tags created on the resources from an entire machine catalog.

This implementation helps in identifying orphaned resources that are created by MCS but no longer used by MCS. This feature is only applicable to **persistent VMs**.

## April 2023

### [Support for customizing power on behavior at storage type change failure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#customize-power-on-behavior-at-storage-type-change-failure)

At power-on, the storage type of a managed disk might fail to change to the desired type due to a failure on Azure. Previously, in these scenarios, the VM would remain off with a failure message sent to you. With this feature, you can either choose to power on the VM even when storage cannot be restored to its configured type or choose to keep the VM powered off

### [Support for Azure disk encryption at host](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#azure-disk-encryption-at-host)

You can now create an MCS machine catalog with encryption at host capability. Currently, MCS supports only the machine profile workflow for this feature. You can use a VM or a template spec as an input for a machine profile.

With this type of encryption, the server hosting the VM encrypts the data and the encrypted data flows through the Azure storage server. Therefore, this method of encryption encrypts data end to end.

### [Support for modifying Azure AD dynamic security group name](https://docs.citrix.com/en-us/citrix-daas/install-configure/create-machine-identities-joined-catalogs/create-azure-ad-joined-catalogs.html#modify-azure-ad-dynamic-security-group-name)

With this feature, you can now modify the Azure AD dynamic security group name associated with a machine catalog. This modification helps you to make the Azure AD dynamic security group information stored in Azure AD identity pool object to be consistent with the information stored in Azure portal.

## March 2023

### [There are several enhancements to Azure Active Directory security group capability](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#march-2023)

The following capabilities have been added:

-  [Support for creating dynamic security group under an existing assigned security group](https://docs.citrix.com/en-us/citrix-daas/install-configure/create-machine-identities-joined-catalogs/create-azure-ad-joined-catalogs.html#create-an-azure-ad-dynamic-security-group-under-an-existing-azure-ad-assigned-security-group) An option, Azure AD security group, is now available when you create Azure AD joined VMs. The option lets you add the VMs to an Azure AD security group based on their naming scheme.
-  [Support for Azure AD dynamic security group for Azure AD joined VM](https://docs.citrix.com/en-us/citrix-daas/install-configure/create-machine-identities-joined-catalogs/create-azure-ad-joined-catalogs.html#azure-active-directory-dynamic-security-group). Citrix now supports dynamic security group for a catalog while creating an MCS machine catalog. Dynamic security group rules place the VMs in the catalog to a dynamic security group based on the naming scheme of the machine catalog.
-  [Support for adding VMs to Azure AD security groups through Full Configuration](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html). Previously, you could create Azure AD dynamic security groups for a machine catalog. With this feature, you can also add an Azure AD dynamic security group under an existing Azure AD assigned security group.

### [Support for changing the storage type of existing VMs to a lower tier on shutdown in Azure environments](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage/manage-machine-catalog-azure.html#change-the-storage-type-of-existing-vms-to-a-lower-tier-on-shutdown)

In Azure environments, you can now save storage costs by changing the storage type of existing VMs to a lower tier when the VMs are shut down. To do this, use the `StorageTypeAtShutdown` custom property.

This is release is allow to set these properties on an existing catalog rather than a new one as specified in an earlier release.

### [Shared tenants for connections](https://docs.citrix.com/en-us/citrix-daas/install-configure/connections.html#edit-connection-settings)

You can now add tenants and subscriptions that share the Azure Compute Gallery with the subscription of the connection. As a result, when creating or updating catalogs, you can select shared images from those tenants and subscriptions.

### [Removed support for changing the OS type for Azure catalogs](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#march-2023)

When changing catalog images, only images with the same OS type as the image in use are shown. With this enhancement, Citrix DaaS no longer supports changing the OS type for Azure catalogs after catalog creation.

## February 2023

### [Support for sharing images across different Azure tenants](https://docs.citrix.com/en-us/citrix-daas/install-configure/connections/connection-azure-resource-manager.html#image-sharing-across-azure-tenants)

Previously, in Azure environments, you could share images only with shared subscriptions using Azure Compute Gallery. With this feature, you can now select an image in Azure Compute Gallery that belongs to a different shared subscription in a different tenant to create and update an MCS catalog.

### [Updates for Autoscale](https://docs.citrix.com/en-us/citrix-daas/manage-deployment/autoscale/autoscale-tagged-machines.html#control-when-autoscale-powers-on-resources)

Whilst not MCS specific, Autoscale ties in nicely here.

Citrix updated the **Control when Autoscale starts powering on tagged machines** option to make it easy to understand. The option controls when Autoscale starts powering on tagged machines based on the percentage of the remaining capacity of untagged machines. When the percentage falls below the threshold (default, 10%), Autoscale starts powering on tagged machines. When the percentage exceeds the threshold, Autoscale goes into power-off mode

### [Real-time GPU Utilization available for AMD GPUs](https://docs.citrix.com/en-us/citrix-daas/monitor/troubleshoot-deployments/machines.html#gpu-utilization)

I am including this one because there are Azure specific considerations here.

You can now see GPU Utilization of AMD Radeon Instinct MI25 GPUs and AMD EPYC 7V12(Rome) CPUs on Monitor. Monitor already supports the NVIDIA Tesla M60 GPUs. GPU Utilization displays graphs with real-time percentage utilization of the GPU, the GPU memory, and of the Encoder and the Decoder to troubleshoot GPU-related issues on multi-session and single-session OS VDAs.

### [Support for scheduling configuration updates in Azure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage/manage-machine-catalog-azure.html#schedule-configuration-updates)

In Azure environments, you can now schedule a time slot for the configuration updates of the existing MCS provisioned machines using the PowerShell command `Schedule-ProvVMUpdate`. Any power on or restart during the scheduled time slot applies a scheduled provisioning scheme update to a machine. You can also cancel the configuration update before the scheduled time using `Cancel-ProvVMUpdate`.

You can schedule and cancel the configuration update of:

-  A single or multiple VMs
-  An entire catalog

### [Support for zone-redundant storage in Azure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#enable-zone-redundant-storage)

Previously, MCS offered only locally-redundant storage. With this feature, zone-redundant storage is now an option in Azure, allowing you to select a storage type depending on what type of redundancy you want to use. Zone-redundant storage replicates your Azure managed disk across multiple availability zones, which allows you to recover from a failure in one zone by utilizing the redundancy in others

## January 2023

### [Option to downgrade storage disk to Standard HDD when VMs shut down](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage/manage-machine-catalog-azure.html#change-the-storage-type-to-a-lower-tier-when-a-vm-is-shut-down)

A new option, `Enable storage cost saving`, is now available on the `Disk Settings` page when you create or update Azure catalogs. The option saves storage costs by downgrading to Standard HDD for the storage disk and the write-back cache disk when the VM shuts down. The VM switches to its original settings on restart.

This is awesome to see come to life - more cost savings

### [Non-persistent VMs are deleted from hypervisors or cloud services when you delete them or their machine catalogs in Full Configuration](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage.html)

The option to retain VMs in hypervisors or cloud services is now available only to persistent VMs

## December 2022

### [Support in MCS for deleting VM objects without accessing the hypervisor](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage.html#delete-machines-without-hypervisor-access)

You can now delete VM objects in MCS without having access to the hypervisor. When deleting a VM or provisioning scheme, MCS needs to remove tags so that the resources are no longer tracked or identified. Previously, if the hypervisor could not be accessed, the tag removal failures were ignored. With this feature, if the hypervisor is not accessible while using the `Remove-ProvVM` command the tag removal will fail, but by using the `PurgeDBOnly` option, you can still delete the VM resource object from the database.

## November 2022

### [Ability to annotate master images extended to catalog creation](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create.html#master-image)

When creating an MCS catalog in Full Configuration, you can now annotate its master image. This was previously only available for updates to an existing Catalog, and whilst not Azure specific, definitely adds value to Azure deployments.

### [Support for changing the storage type to a lower tier when a VM is shut down in Azure environments](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage/manage-machine-catalog-azure.html#change-the-storage-type-to-a-lower-tier-when-a-vm-is-shut-down)

You can now save storage costs by switching the storage type of a managed disk to a lower tier when you shut down a VM. To do this, use the `StorageTypeAtShutdown` custom property. The storage type of the disk changes to a lower tier (as specified in the `StorageTypeAtShutdown` custom property) when you shut down the VM. After you power on the VM, the storage type changes back to the original storage type (as specified in `StorageType` or `WBCDiskStorageType` custom property)

### [Support for updating machine profile and additional custom properties of MCS provisioned machines in Azure environments](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage/manage-machine-catalog-azure.html#update-provisioned-machines-to-current-provisioning-scheme-state)

Previously, in Azure environments, you could use `Request-ProvVMUpdate` to update the `ServiceOffering` custom property of an MCS provisioned machine. Now, you can also update the machine profile and the following custom properties:

-  `StorageType`
-  `WBCDiskStorageType`
-  `IdentityDiskStorageType`
-  `LicenseType`
-  `DedicatedHostGroupId`
-  `PersistWBC`
-  `PersistOsDisk`
-  `PersistVm`

## October 2022

### [Support for using machine profiles and host groups at the same time](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)

When creating a catalog using an Azure Resource Manager master image, you can now use a machine profile and a host group at the same time. This is useful in scenarios where you want to use trusted launch for improved security and at the same time run the machines on dedicated hosts

## September 2022

### [Machine catalogs with Trusted launch in Azure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#machine-catalogs-with-trusted-launch)

You can create machine catalogs enabled with Trusted launch, and use the `SupportsTrustedLaunch` property of the VM inventory to determine the VM sizes that support Trusted launch.

Trusted launch is a seamless way to improve the security of Generation 2 VMs. Trusted launch protects against advanced and persistent attack techniques.

### [Support for machine catalog creation using an image from a different subscription in the same Azure tenant](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#september-2022)

Previously, in Azure environments, you could only select an image within your subscription to create a machine catalog. With this feature, you can now select an image in Azure Compute Gallery (formerly Shared Imaged Gallery) that belongs to a different shared subscription to create and update MCS catalogs.

-  [More information on creating a catalog](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)
-  [Sharing image with another service principal in the same tenant](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#image-sharing-with-another-service-principal-in-the-same-tenant)
-  [Select an image from a different subscription](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#using-powershell-to-select-an-image-from-a-different-subscription)
-  [Azure Compute Gallery](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#azure-shared-image-gallery)

## August 2022

### [Support for using host groups and Azure availability zones at the same time](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#using-host-groups-and-azure-availability-zones-at-the-same-time)

There is now a pre-flight check to assess whether the creation of a machine catalog will be successful based on the Azure availability zone specified in the custom property and the host group’s zone. Catalog creation fails if the availability zone custom property does not match the host group’s zone.

A host group is a resource that represents a collection of dedicated hosts. A dedicated host is a service that provides physical servers that host one or more virtual machines. Azure availability zones are physically separate locations within each Azure region that are tolerant to local failures.

### [Page file setting during image preparation in Azure environments](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#page-file-location)

In Azure environments, you can now avoid potential confusion with the page file location. MCS now determines the page file location when you create the provisioning scheme during image preparation. This calculation is based on certain rules. Features like ephemeral OS disk (EOS) and MCS I/O have their own expected page file location and are exclusive to each other.

If you decouple image preparation from provisioning scheme creation, MCS correctly determines the page file location.

### [Support for updating page file setting in Azure environments](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#update-page-file-setting)

While creating a catalog in an Azure environment, you can now specify the page file setting, including its location and the size, using PowerShell commands. This overrides the page file setting determined by MCS. You can do this by running the `New-ProvScheme` command with the following custom properties:

-  `PageFileDiskDriveLetterOverride`: Page file location disk drive letter
-  `InitialPageFileSizeInMB`: Initial page file size in MB
-  `MaxPageFileSizeInMB`: Maximum page file size in MB

### [Support for enabling Azure VM extensions](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#use-powershell-to-enable-azure-vm-extensions)

When using an ARM template spec as a machine profile to create a machine catalog, you can now add Azure VM extensions to the VMs in the catalog, view the list of supported extensions, and remove extensions you added. Azure VM extensions are small applications that provide post-deployment configuration and automation tasks on Azure VMs. For example, if a VM requires software installation, antivirus protection, or the ability to run a script inside it, you can use a VM extension

### [Trusted launch support for ephemeral OS disk](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)

You can now create provisioning schemes using ephemeral OS disk on Windows with trusted launch. Trusted launch is a seamless way to improve the security of generation 2 VMs. It protects against advanced and persistent attack techniques by combining technologies that can be independently enabled like secure boot and virtualized version of trusted platform module (vTPM

## July 2022

### [Dynamic session timeouts for single-session OS machines](https://docs.citrix.com/en-us/citrix-daas/manage-deployment/autoscale/dynamic-session-timeout.html)

Again, whilst not Azure specific, this has implications on Azure capability. Dynamic session timeouts now support single-session OS machines. A delivery group with at least one VDA of version 2206 or later is required. ***Ensure that those VDAs have registered with Citrix Cloud at least once***

### [Send logoff reminders without forcing user logoff in Autoscale](https://docs.citrix.com/en-us/citrix-daas/manage-deployment/autoscale/force-user-logoff.html)

Whilst not Azure specific, this is still important for Azure based deployments. A new feature is now available in `User Logoff Notifications` (formerly `Force User Logoff`) in Autoscale. The feature provides functionality to send logoff reminders to users without forcing them to log off. Doing that avoids potential data loss caused by forcing users to log off from their sessions

### [Ability to set the Linux OS license type when creating Linux VM catalogs in Azure](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create.html#master-image)

Using the Full Configuration interface, the Linux OS license type can be selected when creating Linux VM catalogs in Azure. There are two choices for bring-your-own Linux licenses: 

-  Red Hat Enterprise Linux
-  SUSE Linux Enterprise Server

### [Support for using ARM template specs as machine profiles](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)

Previously, only VMs could be used as machine profiles. ARM template specs can be used as machine profiles when creating Azure machine catalogs. This allows for taking advantage of Azure ARM template features such as versioning. To ensure that the selected spec is configured correctly and contains required configurations, Citrix perform validation tasks on it. If the validation fails, a different machine profile must be selected

### [Support for validating ARM template spec](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)

Validation of an ARM template spec to make sure that it can be used as a machine profile to create a machine catalog is now available. There are two ways to validate the ARM template spec:

-  Using the Full Configuration management interface
-  Using PowerShell

## June 2022

### [Support for Azure Resource Manager (ARM) template specs when using machine profiles](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)

When using the Full Configuration management interface to select a machine profile for the VMs to inherit configurations from, an ARM template spec can now be selected

### [Ability to change the network setting for an existing provisioning scheme](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage.html#change-the-network-setting-for-an-existing-provisioning-scheme)

The network setting for an existing provisioning scheme can be altered so that the new VMs are created on the new subnetwork. Use the parameter `-NetworkMapping` in the `Set-ProvScheme` command to change the network setting. ***Only the newly provisioned VMs from the scheme will have the new subnetwork settings***. Subnetworks must be under the same hosting unit

### [Retrieve region name information for Azure VMs, managed disks, snapshots, Azure VHD, and ARM template](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#retrieve-region-name--information-for-azure-vms-managed-disks-snapshots-azure-vhd-and-arm-template)

The region name information for an Azure VM, managed disks, snapshots, Azure VHD, and ARM template can now be displayed. This information is displayed for the resources on the master image when a machine catalog is assigned

### [Ability to use machine profile property values in Azure environment](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#retrieve-region-name--information-for-azure-vms-managed-disks-snapshots-azure-vhd-and-arm-template)

While creating an Azure catalog with a machine profile, property values from the ARM template spec or VM, whichever is used as a machine profile, can be set if the values are not explicitly defined in the custom properties. The properties affected by this feature are:

-  Availability zone
-  Dedicated Host Group Id
-  Disk Encryption Set Id
-  OS type
-  License type
-  Service Offering
-  Storage type

If some of the properties are missing from the machine profile and not defined in the custom properties, then the default value of the properties takes place wherever applicable. See [Create a machine catalog using an Azure Resource Manager image](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image) for more information

## May 2022

### [Support for using Set-ProvServiceConfigurationData in Remote PowerShell SDK](https://developer-docs.citrix.com/projects/citrix-virtual-apps-desktops-sdk/en/latest/MachineCreation/Set-ProvServiceConfigurationData)

`Set-ProvServiceConfigurationData` can now be run using Remote PowerShell SDK to apply settings on all applicable parameters. The following list of settings are supported with `Set-ProvServiceConfigurationData`:

-  Change Image Preparation Timeout: `Set-ProvServiceConfigurationData -Name "ImageManagementPrep_PreparationTimeout" -value 60`
-  Skip Enable DHCP: `Set-ProvServiceConfigurationData -Name ImageManagementPrep_Excluded_Steps -Value EnableDHCP`
-  Skip Microsoft Windows KMS Rearm: `Set-ProvServiceConfigurationData -Name ImageManagementPrep_Excluded_Steps -Value OsRearm`
-  Skip Microsoft Office KMS Rearm: `Set-ProvServiceConfigurationData -Name ImageManagementPrep_Excluded_Steps -Value OfficeRearm`
-  Disable preparation VM auto shutdown: `Set-ProvServiceConfigurationData –Name ImageManagementPrep_NoAutoShutdown –Value true`
-  Disable domain injection: `Set-ProvServiceConfigurationData –Name DisableDomainInjection –Value true`

### [Support for Azure Stack HCI provisioning through SCVMM](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/msscvmm.html)

MCS now supports Azure Stack HCI provisioning through Microsoft System Center Virtual Machine Manager (SCVMM). Azure stack HCI clusters can be managed with existing tools including SCVMM

## April 2022

### [Show the progress of catalog creation and updates](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#start-creating-the-catalog)

Full Configuration now shows updates on catalog creation and updates. This displays the overview of the creation and update process, view the history of steps performed, and monitoring of both the progress and running time of the current step

### [Support for creating Azure Active Directory joined machines](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#machine-identities)

An Azure Active Directory joined identity type is now available in Machine Identities when creating a Catalog. With that identity type, MCS can  create machines that are joined to Azure Active Directory. An extra option is available, `Enroll the machines in Microsoft Intune`, to enroll the machines in Microsoft Intune for management.

For information about requirements and considerations related to Azure Active Directory join, see [Azure Active Directory joined detail](https://docs.citrix.com/en-us/citrix-daas/manage-deployment/machine-identities/azure-ad-joined.html)

### [Support for creating hybrid Azure Active Directory joined machines](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#machine-identities)

A Hybrid Azure Active Directory joined identity type is now available in Machine Identities when creating a Catalog. With that identity type, MCS can create hybrid Azure Active Directory joined machines. Those machines are owned by an organization and signed into with an Active Directory Domain Services account that belongs to that organization. Additional information is available for [Hybrid Azure Active Directory joined device provisioning](https://docs.citrix.com/en-us/citrix-daas/manage-deployment/machine-identities/hybrid-azure-ad-joined.html)

### [Azure trusted launch support for snapshots](https://docs.citrix.com/en-us/citrix-daas/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)

 In addition to images, Azure trusted launch is now available for snapshots. If selecting a snapshot with trusted launch enabled, using a machine profile is mandatory. A machine profile with trusted launch enabled must be selected

### [Ability to manage ProvScheme parameters](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create.html#important-consideration-about-setting-provscheme-parameters)

You will now get an error if you set the `New-ProvScheme` parameters in unsupported hypervisors during machine catalog creation or update `Set-ProvScheme` parameters after machine catalog is created

### [Increased resource location limits](https://docs.citrix.com/en-us/citrix-daas/limits.html#resource-location-limits)

Not Azure and MCS specific, but will impact design decisions. Resource Location limits for single-session VDAs and multi-session VDAs are now increased to 10000 and 1000 respectively

### [Machines are not shut down during outages](https://docs.citrix.com/en-us/citrix-daas/whats-new.html#april-2022)

Citrix DaaS now prevents virtual machines from being shut down by the broker when the zone that the machines are in experiences an outage. The machines automatically become available for connections when the outage ends. You don't have to take any action to make the machines available after the outage

### [Updates for Autoscale](https://docs.citrix.com/en-us/citrix-daas/manage-deployment/autoscale/autoscale-tagged-machines.html)

A couple of small changes to Autoscale

-  Renamed **Restrict Autoscale** to **Autoscaling Tagged Machines** to make it easy to understand
-  Added a new option, **Control when Autoscale starts powering on tagged machines**. The option lets you control when Autoscale starts powering on tagged machines based on the usage of untagged machines

### [Support for updating MCS provisioned machines in Azure environments](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-manage.html#update-provisioned-machines-to-current-provisioning-scheme-state)

`Set-ProvScheme` changes the template (provisioning scheme) associated with the catalog, but does not affect existing machines. Using `Request-ProvVMUpdate` command, you can now apply the current provisioning scheme to an existing machine (or set of machines). Currently, the property update supported by this feature is `ServiceOffering`.

This is very handy when you need to change exsting VM sizes within an existing catalog

## March 2022

### [Azure trusted launch support](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)

MCS now supports Azure trusted launch in the Full Configuration management interface. If you choose to select an image with trusted launch enabled, *using a machine profile is mandatory*. This machine profile must have trusted launch enabled

### [Image Portability Service (IPS) is GA](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/migrate-workloads.html)

Whilst not Azure specific, this is heavily Azure focused and will impact MCS capability. IPS simplifies the management of images across platforms. This feature is useful for managing images between an on-premises Resource Location and the public cloud

## February 2022

### [An update to Azure permissions detail](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#about-azure-permissions)

There are two sets of permissions required for security requirements and to minimize risk

-  Minimum permissions: This set of permissions gives better security control. However, new features that require additional permissions will fail because of using minimum permissions
-  General permissions: This set of permissions does not block you from getting new enhancement benefit

### [Use VM's temporary disk to host the write-back cache disk](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)

A new option added: `Use non-persistent write-back cache disk`, to the `Machine Catalog Setup > Disk Settings` page of the `Manage > Full Configuration` interface. Select that option if you do not want the write-back cache disk to persist for the provisioned VMs. With the option selected, the VM’s temporary disk is used to host the write-back cache disk if the temporary disk has sufficient space. Doing this reduces cost

### [Change certain VM settings after creating Azure VM catalogs](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-manage.html#edit-a-catalog)

Using the Full Configuration management interface, you can now change the following settings after creating a catalog:

-  Machine size
-  Availability zones
-  Machine profile
-  Windows licenses

On the Machine Catalogs node, select the catalog and then select Edit Machine Catalog in the action bar.

**Note:** These changes only impact newly reprovisioned machines. Previously created machines remain the same
{:.warning}

### [Support for storing Azure ephemeral OS disk either on the cache disk or temporary disk](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)

It is now supported to store the Azure ephemeral OS disk either on `cache disk` or `temporary disk` for an Azure-enabled virtual machine. You can read more on [Azure Ephemeral Disks with MCS here](https://jkindon.com/citrix-mcs-and-azure-ephemeral-disks/)

## January 2022

### [Ability to specify the Azure secret expiration date for existing connections](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#create-a-service-principal)

Using the Full Configuration management interface, there is now an option to specify the date after which the application secret expires. This is useful as it will prevent being surprise locked out of Azure

-  For service principals created manually in Azure, you can directly edit the expiration date on the `Edit Connection > Connection Properties` page
-  For first-time edits of the expiration date for service principals created through Full Configuration on your behalf, go to `Edit Connection > Edit settings > Use existing`. You can make subsequent edits on the `Edit Connection > Connection Properties` page

## December 2021

### [Web Studio now supports Azure Ephemeral Disk selection](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-image)

Previously, PowerShell was the only choice to create machines that use ephemeral OS disks. There is a new option to select "Azure ephemeral OS disk" in the `Machine Catalog Setup > Storage and License Types` page

## November 2021

### [Annotate an image when updating machines](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-manage.html#update-a-catalog)

When updating an MCS-created catalog, notes can be added to assist with tracking changes. Each time the catalog is updated, a note-related entry is created whether or not a prescriptive note is added. If the catalog is updated without adding a note, the entry appears as null (-).

To view note history for the image, select the catalog, click Template Properties in the lower pane, and then click View note history.

This is not Azure specific, but I am adding this as it's very handy and long awaited

### Support for displaying Azure Marketplace purchase plan information

When creating a machine catalog, you can now view purchase plan information for master images originated from Azure Marketplace images

## October 2021

### [Improve performance by preserving a provisioned VM when power cycling](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#preserving-a-provisioned-virtual-machine-when-power-cycling)

Citrix added a setting `Retain VMs across power cycles` to the Machine Catalog Setup > Disk Settings page of the Full Configuration management interface. The setting lets you preserve a provisioned VM when power cycling in Azure environments.

Be wary of cost implications associated with persistent OS disks

### [Ability to update persistent MCS catalogs](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-manage.html#update-a-catalog)

Citrix introduced the `Update Machines` option for persistent MCS catalogs in the Full Configuration management interface. The option lets customers manage the image or template the catalog uses. When updating a persistent catalog, consider the following:

> Only machines you add to the catalog later are created using the new image or template. We do not roll out the update to existing machines in the catalog

This is signifcant given the [previous method](https://support.citrix.com/article/CTX129205) wasn't easily understood for those not in bed with PowerShell

### [Provision VMs on an Azure dedicated host](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#azure-dedicated-hosts)

Citrix added an option, `Use a host group`, to the Machine Catalog Setup > Master Image page of the Full Configuration management interface. The option lets customers specify which host group they want to use when provisioning VMs in Azure environments

### [Bind a machine catalog to a Workspace Environment Management configuration set](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#workspace-environment-management-optional)

A machine catalog can now be bound to a Workspace Environment Management configuration set on creation. Customers can also choose to bind the catalog after they create the catalog.

Whilst not specifically an MCS feature, it is an enhancement that MCS will consume, so it makes the list of goodies

## September 2021

### [Informative description for image updates](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-manage.html#adding-descriptions-to-an-image)

Change details associated with catalog updates can now be added via PowerShell using the `masterImageNote` attribute. This functionality is useful for administrators who want to add descriptive labels when updating an image used by a catalog. 

Hopefully this lands in the GUI shortly for general consumption

### [Azure VMware Solution (AVS) integration](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-vmware-solution-avs-integration)

Citrix Virtual Apps and Desktops service supports AVS, the Azure VMware Solution. Customers can leverage the Citrix Virtual Apps and Desktop service to use AVS for provisioning workloads in the same they would using vSphere in on-premises environments

### [Same resource group for multiple catalogs](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-resource-groups)

Customers can now use the same resource group for updating and creating catalogs in the Citrix Virtual Apps and Desktops Service. This process:

-  Applies to any resource group that contains one or more machine catalogs
-  Supports resource groups that are not created by Machine Creation Services
-  Creates the VM and associated resources
-  Deletes resources in the resource group when the VM or the catalog is removed

### [Retrieve information for Azure VMs, snapshots, OS disk, and gallery image definition](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#retrieve-information-for-azure-vms-snapshots-os-disk-and-gallery-image-definition)

You can display information for an Azure VM, OS disk, snapshot and gallery image definition. This information is displayed for resources on the master image when a machine catalog is assigned. Use this functionality to view and select either a Linux or Windows image

### [Support for non-domain-joined catalogs](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#machine-identities)

**Destail:** Citrix added an identity type, `Non-domain-joined`, to the Machine Catalog Setup > Machine Identities page of the Full Configuration management interface. With this identity type, MCS can create machines that are not joined to any domain

### Support for using a machine profile when deploying MCS workloads in Azure

This option lets you specify which machine profile you want the image to inherit the configuration from when creating VMs in Azure environments. The image can inherit the following configurations from the selected machine profile:

-  Accelerated networking
-  Boot diagnostics
-  Host disk caching (OS and MCSIO disks)
-  Machine size (unless otherwise specified),
-  Tags placed on the VM

This is awesome. If you have needed to implement my [Accelerated Networking scripts](https://jkindon.com/2020/11/10/enhancing-citrix-mcs-and-microsoft-azure-part-2-accelerated-networking/), then consider using this feature instead

### Support for Windows Server 2022

Requires minimum VDA 2106

## August 2021

### [Support for additional Azure storage types](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#storage-types)

You can now select different storage types for virtual machines in Azure environments using MCS

### [Support for selecting the storage type for write-back cache disks](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#enabling-mcs-storage-optimization-updates)

In the Full Configuration management interface, when creating an MCS catalog, you can now select the storage type for the write-back cache disk. Available storage types include: Premium SSD, Standard SSD, and Standard HDD

## June 2021

### [Access Azure Shared Image Gallery images](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#access-images-from-azure-shared-image-gallery)

When creating a machine catalog, you can now access images from the Azure Shared Image Gallery on the Master Image screen

### Always use standard SSD for an identity disk to reduce cost in Azure environments

Machine catalogs use the standard SSD storage type for identity disks. Azure standard SSDs are a cost-effective storage option optimized for workloads that need consistent performance at lower IOPS levels.

You can read more about the benefits of this change [here](https://jkindon.com/2020/10/27/enhancing-citrix-mcs-and-microsoft-azure-part-1-identity-disk-cost-optimization/) and utilise the provided scripts to convert existing deployments

## May 2021

### [Studio supports selecting Azure Availability Zones](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#provision-machines-into-specified-availability-zones)

Previously, PowerShell was the only choice to provision machines into a specific Availability Zone in Azure environments.

When using Studio to create a machine catalog, you can now select one or more Availability Zones into which you want to provision machines. If no zones are specified, Machine Creation Services (MCS) lets Azure place the machines within the region. If more than one zone is specified, MCS randomly distributes the machines across them

### [Azure ephemeral disk](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-ephemeral-disk)

Citrix Virtual Apps and Desktops service supports Azure ephemeral disk. An ephemeral disk allows you to repurpose the VM cache to store the OS disk for an Azure-enabled virtual machine.

Ephemeral OS disks require that your provisioning scheme use managed disks and a Shared Image Gallery.

### [Improved performance for MCS managed VDAs on Azure](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-throttling)

This enhancement changes the default values for Absolute Simultaneous actions for the hosting connection to 500, and Maximum new actions per minute for the hosting connection to 2,000. No manual configuration tasks are required to take advantage of this enhancement

## April 2021

### [MCS I/O support for Azure VMs without temporary storage](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#machine-creation-service-mcs-storage-optimzation)

MCS I/O now supports machine catalog creation for VMs that do not have temporary disks or attached storage

### Support for Azure Gen2 images

You can now provision a Gen2 VM catalog by using either a Gen2 snapshot or a Gen 2 managed disk to improve boot time performance

### Disabling table storage accounts

Machine Creation Services (MCS) no longer creates table storage accounts for catalogs that use managed disks when provisioning VDAs on Azure

### Eliminating locks in storage accounts

When creating a catalog in Azure using a managed disk, a storage account is no longer created. Storage accounts created for existing catalogs remain unchanged. This change is applicable for managed disks only. For unmanaged disks, there is no change in the existing behavior. Machine Creation Services (MCS) continues creating storage accounts and locks

### [Use a customer-managed encryption key to encrypt data on machines](https://docs.citrix.com/en-us/citrix-daas/install-configure/machine-catalogs-create/create-machine-catalog-citrix-azure.html#azure-server-side-encryption)

Studio adds a setting called Customer-managed encryption key to the Machine Catalog Setup > Disk Settings page. The setting lets you choose whether to encrypt data on the machines to be provisioned in the catalog

## March 2021

### [Azure dedicated hosts](https://docs.microsoft.com/en-us/azure/virtual-machines/dedicated-hosts)

Azure dedicated hosts allow you to provision virtual machines on hardware dedicated to a single customer. While using a dedicated host, Azure ensures that your virtual machines would be the only machines running on that host. This provides more control and visibility to customers thereby ensuring they meet their regulatory or internal security requirements.

A pre-configured Azure host group, in the region of the hosting unit, is required when using the HostGroupId parameter. Also, Azure auto-placement is required. 

When using Azure dedicated hosts, selecting the **Azure Availability Zone** has no effect. The virtual machine is placed by the Azure auto-placement process.

### [Support for Azure server side encryption](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-server-side-encryption)

Citrix Virtual Apps and Desktops service supports customer-managed encryption keys for Azure managed disks. With this support you can manage your organizational and compliance requirements by encrypting the managed disks of your machine catalog using your own encryption key

### [Provision machines into specified availability zones on Azure](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#provision-machines-into-specified-availability-zones)

You can now provision machines into a specific availability zone in Azure environments. With this functionality You can specify one or multiple Availability Zones on Azure. Machines are nominally equally distributed across all provided zones if more than one zone is provided The virtual machine and the corresponding disk are placed in the specified zone (or zones)

### [Azure Shared Image Gallery](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-shared-image-gallery)

Citrix Virtual Apps and Desktops service supports Azure Shared Image Gallery as a published image repository for MCS provisioned machines in Azure. Administrators have the option of storing an image in the gallery to accelerate the creation and hydration of OS disks. This process improves the boot and application launch times for non-persistent VMs

## February 2021

### [Support for Azure Gen2 images](https://docs.microsoft.com/en-us/azure/virtual-machines/generation-2)

You can now provision managed disks using Gen2 VMs in Azure environments to improve boot time performance

### [Extended support for Citrix Managed Azure](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/#citrix-managed-azure)

Citrix Managed Azure is now available in the following Citrix Virtual Apps and Desktops service editions: Standard for Azure, Advanced, Premium, and Workspace Premium Plus

### [Support for placing master images in Azure Shared Image Gallery](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-master-image)

Studio now provides you an option to place master images in Azure Shared Image Gallery (SIG). SIG is a repository for managing and sharing images. It lets you make your images available throughout your organization.

Citrix recommend that you store a master image in SIG when creating large non-persistent machine catalogs because doing that enables faster reset of VDA OS disks

### [Retain system disk for MCS machine catalogs in Azure](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#enabling-mcs-storage-optimization-updates)

Studio now lets you control whether to retain system disks for VDAs during power cycles. Ordinarily, the system disk is deleted on shutdown and recreated on startup. This ensures that the disk is always in a clean state but results in longer VM restart times. If system writes are redirected to the cache and written back to the cache disk, the system disk remains unchanged.

To avoid unnecessary disk recreation, use the Retain system disk during power cycles option, available on the Machine Catalog Setup > Disk Settings page. Enabling the option reduces VM restart times but increases your storage costs. The option can be useful in scenarios where an environment contains workloads with sensitive restart times

### [Studio now supports creating MCS machine catalogs with persistent write-back cache disk](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#enabling-mcs-storage-optimization-updates)

Previously, PowerShell was your only choice to create a catalog with persistent write-back cache disk. You can now use Studio to control whether the write-back cache disk persists for the provisioned VMs in Azure when you are creating a catalog. If disabled, the write-back cache disk is deleted during each power cycle to save storage costs, causing any data redirected to the disk to be lost.

To retain the data, enable the Use persistent write-back cache disk option, available on the Machine Catalog Setup > Disk Settings page.

## January 2021

### [Azure Shared Image Gallery](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#configure-shared-image-gallery)

**Details:**  Citrix Virtual Apps and Desktops service supports Azure Shared Image Gallery as a published image repository for MCS provisioned machines in Azure. Administrators have the option of storing an image in the gallery to accelerate the creation and hydration of OS disks from the master image. This process improves the boot and application launch times for non-persistent VMs

## December 2020

### [Standard SSD disk type support for Azure](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#create-a-machine-catalog-using-an-azure-resource-manager-master-image)

**Details:**  Studio now adds support for standard SSD disk type. Azure standard SSDs are a cost-effective storage option optimized for workloads that need consistent performance at lower IOPS levels

## October 2020

### [Use direct upload for Azure managed disks](https://azure.microsoft.com/en-us/blog/introducing-the-preview-of-direct-upload-to-azure-managed-disks/)

**Details:**  Direct upload eliminates the need to attach an empty managed disk to a virtual machine. Directly uploading to an Azure managed disk simplifies the workflow by enabling you to copy an on-premises VHD directly for use as a managed disk. Supported managed disks include Standard HDD, Standard SSD, and Premium SSD

### [Single Resource Group in Azure](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html)

**Details:** You can now create and use a single Azure resource group for updating and creating catalogs in Citrix Virtual Apps and Desktops. This enhancement applies to both the full scope and narrow scope service principals. The previous limit of 240 VMs per 800 managed disks per Azure Resource Group has been removed. There is no longer a limit on the number of virtual machines, managed disks, snapshots, and images per Azure Resource Group

## September 2020

### Support for a new machine type

**Details:** This release adds support for the NV v4 and the DA v4 series of AMD machines, when configuring Premium Disks for a machine catalog

## August 2020

### [Improved boot performance for Azure system disks](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/machine-catalogs-create.html#improve-boot-performance)

**Details:** This release supports improved boot performance for Citrix Cloud implementations using Azure when MCSIO is enabled. With this support, you can retain the system disk. This provides the following advantages:

-  VMs and applications now boot and launch with performance similar to how the golden image is served
-  Reduction in API quota consumption, deleting and creating the system disk, and state transition delay caused when you delete a VM

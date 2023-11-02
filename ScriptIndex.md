---
layout: page
title: Script Index
subtitle: Scripts that may be of interest
---

-  this unordered seed list will be replaced by the toc
{:toc}

Here is a script index. Hopefully these are of use to some folks. They have been of use to me.

## Nutanix Scripts

-  **[UpdatePCVMCategories.ps1](https://github.com/nutanixdev/euc-samples/tree/main/citrix/categories/manage_pc_vm_categories)**. Use PowerShell to assign VM Categories in Prism Central. [Info](https://www.nutanix.dev/2023/07/26/)
-  **[ReplicateCitrixBaseImageVM.ps1](https://github.com/nutanixdev/euc-samples/tree/main/citrix/mcs/replicate_citrix_base_image_pd)**. Use PowerShell to replicate Gold Images across Nutanix Clusters via Prism Element Protection Domains. [Info](https://www.nutanix.dev/2023/07/03/replicating-images-for-citrix-mcs-with-prism-element-protection-domains-and-powershell/)
-  **[ReplicateCitrixBaseImageVMAPI.ps1](https://github.com/nutanixdev/euc-samples/tree/main/citrix/mcs/replicate_citrix_base_image_pd_api)**. Use PowerShell to replicate Gold Images across Nutanix Clusters via Prism Element Protection Domains via API. [Info](https://www.nutanix.dev/2023/07/03/replicating-images-for-citrix-mcs-with-prism-element-protection-domains-and-powershell/)
-  **[ReplicateCitrixBaseImageRP.ps1](https://github.com/nutanixdev/euc-samples/tree/main/citrix/mcs/replicate_citrix_base_image_pc/recovery_point_replication)**. Use PowerShell to replicate Gold Images across Nutanix Clusters via Prism Central Protection Policies. [Info](https://www.nutanix.dev/2023/07/04/replicating-images-for-citrix-mcs-with-prism-central-protection-policies-and-recovery-points/)
-  **[UpdateCVADHostedMachineId.ps1](https://github.com/nutanixdev/euc-samples/tree/main/citrix/multi_cluster_migration/citrix_cvad_reset_hostedmachineid)**. Migrate Citrix workloads across Nutanix Clusters and update hosting details for CVAD.
-  **[UpdateDaaSHostedMachineId.ps1](https://github.com/nutanixdev/euc-samples/tree/main/citrix/multi_cluster_migration/citrix_daas_reset_hostedmachineid)**. Migrate Citrix workloads across Nutanix Clusters and update hosting details for DaaS.

## Azure Scripts

-  **[ChangeVMAz.ps1](https://github.com/JamesKindon/Azure/blob/master/ChangeVMAz.ps1)**. Switch a VMs Availability Zone
-  **[MigrateVMAvailabilitySet.ps1](https://github.com/JamesKindon/Azure/blob/master/MigrateVMAvailabilitySet.ps1)**. Move a VM to a different Availability Set
-  **[RemoveVMAvailabilitySet.ps1](https://github.com/JamesKindon/Azure/blob/master/RemoveVMAvailabilitySet.ps1)**. Remove a VM from an Availability Set
-  **[ReplicateAzureSnapshot.ps1](https://github.com/JamesKindon/Azure/blob/master/ReplicateAzureSnapshot.ps1)**. Replicate an Azure Snapshot across Multiple Regions and Subscriptions. [Info](https://jkindon.com/microsoft-azure-snapshot-replication-framework/)
-  **[ShrinkAzureOSDisk.ps1](https://github.com/JamesKindon/Azure/blob/master/ShrinkAzureOSDisk.ps1)**. Shrink an OS disk for Ephemeral Use. [Info](https://jkindon.com/shrink-azure-os-disk-for-ephemeral/)
-  **[JoinStorageAccountToDomain.ps1](https://github.com/JamesKindon/Azure/tree/master/JoinStorageAccountToDomain)**. Join a Storage Account to an Active Directory Domain. [Info](https://jkindon.com/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads/)

## Citrix Scripts

-  **[CheckBrokerService.ps1](https://github.com/JamesKindon/Citrix/blob/master/CheckBrokerService.ps1)**. Checks the Citrix Broker Desktop Service is started after the predefined sleep time. Uses as a Startup Script.
-  **[CreateDesktopsForVDAs.ps1](https://github.com/JamesKindon/Citrix/blob/master/CreateDesktopsForVDAs.ps1)**. Creates a Tag per VDA and Creates a dedicated desktop to launch only against that Tag.
-  **[EnableSSL.ps1](https://github.com/JamesKindon/Citrix/blob/master/EnableSSL.ps1)**. Handles the assignment of certificates requried for both Citrix Brokers and Citrix Cloud Connectors as well as enabling or disabling HTTP based XML Access.
-  **[GetUserSessions.ps1](https://github.com/JamesKindon/Citrix/blob/master/GetUserSessions.ps1)**. Script to hunt for sessions based on StoreFront launch points.
-  **[PreFetchStartApps.ps1](https://github.com/JamesKindon/Citrix/blob/master/PreFetchStartApps.ps1)**. Startup Script to deal with Application pre-launch at machine startup.
-  **[WeeklyRebootSchedulesByTag.ps1](https://github.com/JamesKindon/Citrix/blob/master/WeeklyRebootSchedulesByTag.ps1)**. Creates a Tag driven daily reboot schedule using `BrokerRebootScheduleV2` schedules.
-  **[MigrateDedicatedMachines.ps1](https://github.com/JamesKindon/Citrix/tree/master/Migration%20Scripts/MigrateDedicatedMachines/MigratedDedicatedVM)**. Migrates persistent VMs (including MCS provisioned) to a different CVAD Site and retains all power management.
-  **[MigrateDedicatedMachinesDaaS.ps1](https://github.com/JamesKindon/Citrix/tree/master/Migration%20Scripts/MigrateDedicatedMachines/MigrateDedicatedVMDaaS)**. Migrates persistent VMs (including MCS provisioned) to aCitrix Cloud DaaS tenant and retains all power management.
-  **[MigrateMCSToManual.ps1](https://github.com/JamesKindon/Citrix/blob/master/Migration%20Scripts/MigrateMCSToManual/MigrateMCSToManual.ps1)**. Migrates Dedicated MCS provisioned machines to a manual provisioned catalog and delivery group and retains power management.
-  **[SwitchAzureHostingConnections.ps1](https://github.com/JamesKindon/Citrix/blob/master/Migration%20Scripts/SwitchAzureHostingConnections/SwitchAzureHostingConnections.ps1)**. Switches Hypervisor connection objects in Azure to support ASR based failovers for dedicated/Persistent virtual machines.
-  **[Misc Migration Scripts](https://github.com/JamesKindon/Citrix/tree/master/Migration%20Scripts)**. Export and Import functionality for common Citrix items.
-  **[RestartWEMServices.ps1](https://github.com/JamesKindon/Citrix/blob/master/Citrix%20WEM%20Startup%20Scripts/RestartWEMServices.ps1)**. Startup script that stops and starts WEM services and forces a cache refresh.
-  **[ConvertIdentityDisks.ps1](https://github.com/JamesKindon/Citrix/blob/master/Azure/ConvertIdentityDisks.ps1)**. Loops through a list of Azure subscriptions searching for Citrix Identity Disks and attempts to convert them to the specified Disk Sku. [Info](https://jkindon.com/enhancing-citrix-mcs-and-microsoft-azure-part-1-identity-disk-cost-optimization/)
-  **[CreateOSSnapshot.ps1](https://github.com/JamesKindon/Citrix/blob/master/Azure/CreateOSSnapshot.ps1)**. Creates an Azure OS Disk snapshot for Citrix MCS use based on a provided VM name.
-  **[EnableAcceleratedNetworking.ps1](https://github.com/JamesKindon/Citrix/blob/master/Azure/EnableAcceleratedNetworking.ps1)**. Loops through a list of Azure Resource Groups and Subscriptions, searching for network interfaces that are not enabled for accelerated networking. [Info](https://jkindon.com/enhancing-citrix-mcs-and-microsoft-azure-part-2-accelerated-networking/)
-  **[EnableManagedIdentity.ps1](https://github.com/JamesKindon/Citrix/blob/master/Azure/EnableManagedIdentity.ps1)**. Loops through a list of Azure Resource Groups and Subscriptions, grabs the virtual machines and assigns a UserAssigned managed identity. [Info](https://jkindon.com/enhancing-citrix-mcs-and-microsoft-azure-part-3-managed-identities/)
-  **[UpdateMCSCatalogViaAPI.ps1](https://github.com/JamesKindon/Citrix/blob/master/Azure/UpdateMCSCatalogViaAPI.ps1)**. Updates a Citrix Cloud MCS catalog with an Azure image via API.
-  **[WEMSelectiveActionTrackingReset.ps1](https://github.com/JamesKindon/CitrixWEMActionTrackingReset/blob/master/WEMSelectiveActionTrackingReset.ps1)**. A simple Powershell script to manage the registry caching of tracked WEM actions. [Info](https://jkindon.com/selective-deletion-of-the-wem-actions-tracking-cache/)

## Misc EUC Scripts

-  **[SetShellFolderDefaults.ps1](https://github.com/JamesKindon/Citrix/blob/master/SetShellFolderDefaults.ps1)**.  Script to reset the shell folder keys associated with folder redirections. Idea is to avoid having to use GPO to force data back to default locations (avoid CSE processing).

-  **[DistributeContainerShares.ps1](https://github.com/JamesKindon/Citrix/blob/master/FSLogix/DistributeContainerShares.ps1)**. Builds on the work of Ryan Revord to order FSLogix Share locations by available space.
-  **[GetContainerSizes.ps1](https://github.com/JamesKindon/Citrix/blob/master/FSLogix/GetContainerSizes.ps1)**. List and export container sizes from a share.

## BIS-F Sealing Scripts

-  **[BISF_ConnectWiseControl.ps1](https://github.com/JamesKindon/Citrix/tree/master/Sealing/Connectwise)**. Custom script to handle Connect Wise prep tasks. To be executed via BIS-F.
-  **[BISF_Kaseya.ps1](https://github.com/JamesKindon/Citrix/tree/master/Sealing/Kaseya)**. Custom script to handle Kaseya prep tasks. To be executed via BIS-F.
-  **[BISF_PolicyPak.ps1](https://github.com/JamesKindon/Citrix/tree/master/Sealing/PolicyPak)**. Custom script to handle PolicyPak prep tasks. To be executed via BIS-F.

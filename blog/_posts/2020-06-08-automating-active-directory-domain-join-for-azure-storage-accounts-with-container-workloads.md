---
layout: post
title: "Automating Active Directory Domain Join for Azure Storage Accounts with Container Workloads"
permalink: "/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads/"
description: Leveraging PowerShell to Automate AD Join tasks for Azure Storage Accounts
categories: [Apps and Desktops, Azure, Citrix, Cloud, FSLogix, PowerShell, Profiles, Windows]
redirect_from: 
    - /2020/06/08/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads
    - /2020/06/08/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads/
image:
  path: /assets/img/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Having the ability to Active Directory Domain Join (ADDS) an Azure Storage account has changed the game for many organizations deploying file service into Azure. I wrote previously about the [options for storing container workloads such as FSLogix Containers in Azure](https://jkindon.com/2020/05/27/navigating-azure-storage-options-for-fslogix-containers/), one of them being native Domain Joined Storage accounts.

There are a few steps in Domain Joining and configuring the Storage Accounts, which can be a little confusing the first time you run through the process. Microsoft provides some [OK documentation and some baseline PowerShell modules](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-identity-ad-ds-enable) which drives the process (AzureFilesHybrid) and [Mr Brinkhoff](https://twitter.com/Brinkhoff_C) has a [good guide](https://www.christiaanbrinkhoff.com/2020/03/01/learn-here-how-to-configure-azure-files-with-active-directory-ad-authentication-for-fslogix-profile-container-and-msix-app-attach/) to help you out from a base walkthrough. If you get any of the process wrong, or it fails at all, it can be a little finicky to fix (depends how far you get)

I have put together a script to help automate the entire process, specifically aimed at configuring Storage Accounts for FSLogix Container workloads to support your EUC environment. The script automates the following components for you

1.  Downloads and installs the AZFilesHbrid PowerShell Module if it does not already exist
2.  Domain Joins an existing Azure Storage Account
3.  Configures the required IAM roles for specified users (such as WVD or Citrix users) to access the share, as well as configuring specified Admin roles to allow for NTFS permission management (Elevated Contributor)
4.  Configures the required NTFS permissions on the root share to allow for FSLogix Container creation (via the `new-smbconnection` command and the storage account key from the specified storage account)

The script contains a couple of basic parameters allowing you to break out the tasks as required, or simply alter an existing configuration.

-  `JoinStorageAccountToDomain` simply grabs the defined Storage Account and Active Directory Domain Joins into the specified OU. I default to using a computer account given the incoming changes and Microsoft guidance (make sure your storage account doesn't have more than 15 characters!)
-  `ConfigureIAMRoles` sets the _Storage File Data SMB Share Contributor role_ for the specified user groups (think of this as the share permission on-premises) and the _Storage File Data SMB Share Elevated Contributor role_ for the specified administrators (this is required for admins to alter NTFS permissions if not using the storage account keys to map the drive and set them)
-  `ConfigureNTFSPermissions` sets a base level of NTFS permissions for FSLogix Container access via the `new-smbmapping` command leveraging the Azure storage account access key (I look for the default "Key1") and utilizing icacls
-  `DebugStorageAccountDomainJoin` debugs any Domain Join issues (this is just calling the AZFilesHybrid Debug commands)

You will need to alter some basic variables for your environment in the script, it’s very self-explanatory, but a brief outline as follows:

-  `SubscriptionId` Your Azure subscription ID
-  `ResourceGroupName` The Resource Group your storage account resides in
-  `StorageAccountName` The name of your storage account
-  `ShareName` The Share name defined in your storage account (Eg, Containers)
-  `DomainAccountType` Either a `ServiceLogon` or `ComputerAccount`. I default to `ComputerAccount` (Updated this 23.09.2020)
-  `OU` The OU for the storage account `ServiceLogon` or `ComputerAccount` to be created. I default to using the `OrganizationalUnitDistinguishedName` switch, so you need to specify your OU by its **_Distinguished Name_**
-  `FSContributorGroups` Groups to assign the **_Storage File Data SMB Share Contributor_** roles (Such as Citrix or WVD user groups)
-  `FSAdminUsers` Admin users to assign the additional **Storage File Data SMB Share Elevated Contributor** Roles (admins who can change NTFS permissions on the root share). I was too lazy to split out the roles so the admins also get **_Storage File Data SMB Share Contributor_** and **_Storage File Data SMB Share Reader_**
-  `DownloadUrl` The current download URL for the current **AZFilesHybrid** Module
-  `ModulePath` Path to download and load the **AZFilesHybrid** Module
-  `DriveLetter` Drive letter to use in the `new-smbmapping` session to set the default NTFS permissions for containers. I default to X the drive is cleared post setting permissions
-  `JSON` allows for the input to be JSON based rather than defining variables in the script
-  `JSONInputPath` specifies the JSON input file

At the end of a run using the `JoinStorageAccountToDomain`, `ConfigIAMRoles` and `ConfigNTFSPermissions` parameters, you should have a Domain Joined Storage Account

[![Domain joined to Kindo.com]({{site.baseurl}}/assets/img/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads/DomainJoinKindon.png)]({{site.baseurl}}/assets/img/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads/DomainJoinKindon.png)

With IAM roles configured on the file share

[![IAM Roles in place]({{site.baseurl}}/assets/img/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads/IAMRoles.png)]({{site.baseurl}}/assets/img/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads/IAMRoles.png)

And appropriate NTFS permissions for your Containers

[![IAM Roles in place]({{site.baseurl}}/assets/img/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads/NTFS.png)]({{site.baseurl}}/assets/img/automating-active-directory-domain-join-for-azure-storage-accounts-with-container-workloads/NTFS.png)

## Summary and Script

I hope this helps those of you that are looking to consume Azure Domain Joined Storage Accounts for Container workloads. The script is located on my [GitHub repo here](https://github.com/JamesKindon/Azure/tree/master/JoinStorageAccountToDomain) – any suggestions or additions are welcome. There are a few more things I would like to add to it such as cleaning failed joins, but I wanted to limit the number of modules and dependencies required for now.

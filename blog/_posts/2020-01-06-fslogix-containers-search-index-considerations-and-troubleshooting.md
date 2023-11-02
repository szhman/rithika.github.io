---
layout: post
title: "FSLogix Containers – Search Index Considerations and Troubleshooting"
permalink: "/fslogix-containers-search-index-considerations-and-troubleshooting/"
description: "A re-post of shared article removed when FSLogix was acquired by Microsoft - deliving into search troubleshooting with FSLogix"
categories: [FSLogix, Profiles, Search, Start Menu, Windows]
redirect_from: 
    - /2020/01/06/fslogix-containers-search-index-considerations-and-troubleshooting
    - /2020/01/06/fslogix-containers-search-index-considerations-and-troubleshooting/
image:
  path: /assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

This post is not addressing Windows 10 Multi-Session or Windows Server 2019 Per-User Search. For changes with these Operating Systems, please see my follow up post [here](https://jkindon.com/2020/03/15/windows-search-in-server-2019-and-multi-session-windows-10/)
{:.warning}

This post was initially posted on the FSLogix Blog which has been recently removed. I thought it was worth reposting as the content is still relevant. Extremely important to me is that credit is sent where it should be, this post was a collaboration between four individuals:

-  [James Kindon](https://twitter.com/james_kindon)
-  [James Rankin](https://twitter.com/james____rankin)
-  [Gareth Carson](https://twitter.com/CitXen)
-  [Nigel Woods](https://twitter.com/NigelWoods27)

------------------------------------------------------------------------------------------------------------------------------------

Windows Search is a feature that has existed in the Windows OS since Windows Vista, although it did exist as freeware called Windows Desktop Search for XP and Server 2003. It replaced the Indexing Service that first arrived as part of the Windows NT4 Option Pack, which was deprecated in Windows 7 and finally removed in Windows 8.

Windows Search builds a full-text index of files on a device. The time required for the initial creation of this index depends on the amount and type of data to be indexed and can take up to several hours. In persistent environments, this does not present a problem, as the creation of the index is a one-time event. However, in non-persistent RDSH or VDI environments like XenApp and XenDesktop, this can present an issue.

Also, the Search database is normally placed in a device-specific area - `C:\ProgramData\Microsoft\Search\Data\Applications\Windows\Windows.edb`. As well as being shared amongst users, it can also grow very large in some situations. Trying to "roam" this data by redirecting the Registry or using symbolic links can be very difficult, as Search is not intended to be used from networked locations. All of these issues are the drivers behind the FSLogix feature which allows you to mount the Search database to a VHD – Search Roaming.

Whilst it normally "just works", we decided to put this post together in an attempt to help clarify how the search roaming feature of FSLogix fits together, and how you can troubleshoot not only initial configurations but issues that may arise within everyday use.

## Search Modes

FSLogix offers two modes of Search Roaming, *Single User Search* and *Multi-User Search*. Let's start by clarifying the two modes that you may come across when using FSLogix Profile Containers or Office Containers.

### Single User Search

Single User Search roams the entire Search Database in the user's Profile Container or Office Container. So the `.edb` file from the ProgramData folder is mounted into either the user's Profile Container or Office Container. The redirected `.edb` and the necessary files to support search are stored in the `\WSearch` folder in the VHD(X).

Roaming the Windows Search database means that Windows Search is available immediately after logon and no re-indexing needs to take place. Requirements:

-  Supported on Windows 7 and later and Server 2008 R2 and later
-  Requires an Office 365 Container Licence

### Multi-User Search

Multi-User Search moves the user portion of the search index out and stores it in the user's Profile Container or Office Container. This uses FSLogix's secret sauce to capture the user-specific portion of the `.edb` file, which handles Outlook searching, and mount it into the Profile Container or Office Container as appropriate. The user-specific portion of the .edb and the necessary files to support search are stored in the `\WSearch` folder in the VHD(X).

This feature allows roaming a user's Outlook Search information. This feature can be used on single-user (XenDesktop, VDI, etc.) or multi-user systems (RDSH, XenApp, etc.) but only the Outlook email Search information is roamed. Requirements:

-  Supported on Windows 8 and later, Windows Server 2012 R2 and later, both 32 and 64 bit
-  Supports Outlook 2010 and later
-  Requires an Office 365 Container Licence

## Search Roaming Dependencies

For roaming to successfully occur, the following dependencies exist:

-  Windows Search Service is started, set to automatic. Do not set delayed start. Also, pay attention to the section "Operating System Considerations" to understand any potential differences between `Search service` and `Search service feature`
-  Provisioning Server Optimization disables this service - use a GPO to revert this setting and enforce the search service to start. We have a feature request in with BIS-F dev team to assist with this
-  Office Container Licence – The FSLogix Office Container is a subset of the [FSLogix Profile Container](https://docs.microsoft.com/en-us/fslogix/configure-profile-container-tutorial) feature plus the ability (if acquired) to [roam the Windows Search database](https://docs.microsoft.com/en-us/fslogix/configure-search-roaming-ht) with the user.  Office Containers solve the issues that Office 365 has in a roaming profile environment.  Office Containers can be used with any 3rd party profile solution

## Windows Search – Operating System Considerations

There is an important distinction to be made when dealing with the terminology surrounding the term "Windows Search" as there are some notable differences across operating system versions within the Microsoft stack, particularly on Server operating systems.

### Windows 7/8/8.1/10

Client operating systems are simple enough, the Windows Search `service` simply needs to be running and set to Automatic start. This configuration is for Single User search only.

[![Search Service Properties – Windows Server 2008 R2]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/SearchServiceStatus.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/SearchServiceStatus.png)

### Windows Server 2008 R2

On 2008 R2, there are two File Services roles available, the `Indexing Service` and the `Windows Search Service`. These could not be installed on the same machine. The `Indexing Service` merely installed a service named the same.

The `Windows Search Service` did, well, nothing that we could see. The indication was that it allowed clients to do fast searches of server shares. The description, as you can see below, indicates that it isn't intended for enterprise scenarios.

[![legacy Windows Search Service Role]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/LegacySearchService.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/LegacySearchService.png)

FSLogix **<u>does not</u>** have the ability to roam search when utilising containers against Server 2008 R2. Technically, FSLogix can support single-user search only on Windows Server 2008 R2, but as you can imagine, this use case is limited specifically to DaaS providers who provide Windows Server 2008 R2 instances on a one-to-one basis, and not for RDSH or XenApp.

### Windows Server 2012 R2

On 2012 R2, you need to install the `feature` to enable the `service`. Without the `feature`, the `service` doesn't exist. The two are one and the same on this OS.

[![Modern Windows Search Service Role]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ModernSearchFeature.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ModernSearchFeature.png)

Install the `feature`, and the `"Windows Search"` `service` appears in services.msc. The old `"Indexing Service"` does not exist at all.

[![Search Service Properties - Windows Server 2012 R2]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ModernSearchService.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ModernSearchService.png)

### Windows Server 2016

On Server 2016, however, the `"indexing service"` is now called the `"Windows Search"` service and is installed by default (although disabled).

If you enable the `service` and start it, indexing begins. Also, when started it also allows clients (even Windows 7 clients) to access the index and do file content searches of shares on the server.

However, there is still the `"Windows Search Service"` `feature` which you can install from Server Manager, and this does <u>not</u> enable the `service`, and even when installed, the index still shows as "not running" when you do a search (however, if you have the `"Windows Search"` `service` (`service`, <u>not</u> `feature`!) set to Manual, it will auto-start the service when you do a search).

[![Windows Search Role in Server 2016]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/SearchService2016.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/SearchService2016.png)

[![Search Service Properties Windows Server 2016]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/SearchService2016Status.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/SearchService2016Status.png)

To summarise the above into simple terms, below is what you will need to action per Operating System for Multi-User Search

[![Multi-User Search OS Table]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/MultiSearchOSTable.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/MultiSearchOSTable.png)

## Microsoft Office Considerations

When dealing with Outlook Indexing, there are a few things to take note of:

-  Microsoft Office must be installed whilst the Search Service is running, else the search components for outlook will be missing
-  If you are retrofitting Windows Search into an already existing environment, then you will need a full office repair to have the search capability enabled and operational from an office perspective

## Enabling Search Roaming

Believe it or not, this one is a common miss. There are numerous ways to enable all settings with FSLogix, either via an ADMX file (GPO) which is a combination of native ADMX capability and simple registry stamping, or native registry entries, see my post around [configuration options](https://jkindon.com/2018/02/20/getting-to-know-fslogix-containers/). Ideally, you are using the latest ADMX (and latest FSLogix release of course with all the patches….) and have enabled search roaming in all the appropriate locations.

Turn it on and note that you also have to configure the appropriate location to store the database

[![Enable Search Roaming (Bake this into master image)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/GPOSearchRoaming.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/GPOSearchRoaming.png)

For Office Containers

[![Store Search in Office Container]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/GPOO365SearchRoam.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/GPOO365SearchRoam.png)

And for Profile Containers

[![Store Search in Profile Container]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/GPOProfileSearchRoam.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/GPOProfileSearchRoam.png)

Note that we will discuss the differences in behaviours and considerations around when you are using both containers together shortly.

Note that if you misconfigure or do not configure these settings, your log file will clearly tell you what's going on.

[![Search Logs]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/SearchLogs.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/SearchLogs.png)

## Log Files

FSLogix provides full detailed logging out of the box for all operations. You just need to know where to find these log files, by default they are stored in the following location on each endpoint

`C:\ProgramData\FSLogix\Logs\`

[![Log Directory]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/LogDirectory.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/LogDirectory.png)

The logs we are interested in for this post are obviously the Search Logs, however, depending on your deployment and use of containers, you will also be interested in the ODFC (office containers) and the Profile logs at a minimum.

These logs can all be parsed utilising your choice of log viewer (Notepad++ anyone?) or there is a very handy little utility called FRXTray.exe (It will launch directly to your notification area). You will be presented with a basic view to start with, click advanced to get the goods

[![Standard Tray View]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/FRXTrayStandard.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/FRXTrayStandard.png)

[![Advanced FRX Tray View]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/FRXTrayAdvanced.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/FRXTrayAdvanced.png)

Two important points at this stage

1.  The log viewer doesn't (at time of writing) show you the Search-based logs - you need to parse these manually
2.  The traffic light indicator will function in the following way
    -  No Containers Active = RED
    -  Office 365 Container Only = Yellow
    -  Profile Containers = Green

The utility by default Is found here: `C:\Program Files\FSLogix\Apps\frxtray.exe` and we like to as practice make it available on logon using something like Citrix WEM, or at least have it published and available in the users start menu.

A good example of a working search catalogue mount will be shown as follows:

[![Successful Search Operations]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/SuccessfullSearchLog.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/SuccessfullSearchLog.png)

The above process shows the following successful tasks

1.  Confirming Multi-User Search Configuration
2.  When the User Logs in, the container is mounted, and the search mount process begins. We can see that a database is found for the user, and the catalog is successfully mounted
3.  A basic ICACLS permissions reset is performed on the WSearch Index location
4.  When the user logs off, the catalog is gracefully dismounted

Now, in contrast, let's look at a broken request

[![Search Issues]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/FailedSearchLog.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/FailedSearchLog.png)

Here we can see some pretty nice problems

1.  The search database is located in the container, however
2.  It cannot be mounted for some reason - this is the great unknown in some of these deployments. What happened to the index to break it so that FSLogix cannot mount it?
3.  Despite the fact that the catalogue fails to mount, a basic ICACLS is again run on the WSearch folder to grant the user full permissions

It's once you have seen this behaviour or set of problems that you start looking at the actual index files being a problem. As you can already tell above, the index mounts for one user, so you know configurations are OK, Outlook should be happy, and all your enablement policies are configured OK

## Multi-Container Behaviour

The behaviour of where the search database can be stored is configurable by the admin when both Profile Containers and Office Containers are in play.

### Single User Search

If both the FSLogix Profile Container feature and the Office Container feature are configured to have the Search database, the system will put the database into the Profile container by default The admin is able to specify either the Profile container or the container where the Search database will be stored. [Single User Search reference](https://docs.microsoft.com/en-us/fslogix/configure-search-roaming-ht#configure-single-user-search)

### Multi-User Search

It is possible to choose where the search Database is stored for Multi-User Environments, it can be stored in either the Profile Container or the Office 365 Container. [Multi-User Search reference](https://docs.microsoft.com/en-us/fslogix/configure-search-roaming-ht#configure-multi-user-search)

When the multi-user search is enabled, the following registry value is changed from `0` to `1`in  `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Search\Preferences\EnablePerUserCatalog(REG_DWORD)`

[![Multi-User Search Reg Change]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/PerUserCatalog.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/PerUserCatalog.png)

## Resetting the Search Index

Being what it is, the search index may corrupt over its lifetime for a myriad of reasons… locations not being available (rare), permissions corruptions, upgrades to the search service due to patches etc. It's quite daunting trying to figure out what is what and how to reset things. You have a couple of options

### FSLogix FRX reset tool

This utility is designed to queue a windows-based search reindex for the specified user when using multi-user search.

> *frx reset-user-search-db -user domain\user*

**Manual reset**. **(hammer approach)** This approach involves the process of mounting the users' profile disk using the FSLogix Context Menu Handler tool `C:\Program Files\FSLogix\Apps\frxcontext.exe` and manually trawling through to locate and kill the search index files which are stored here `%AppData%\Roaming\FSlogix\WSearch` when multi-user search is enabled

### FSLogix Context Menu Handler

First, you must install this utility using the command `frxcontext.exe --install`

[![frxcontext.exe install]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/FRXContext.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/FRXContext.png)

This now allows the mounting of the VHD/VHDX file via the right-click context menu using the **Mount for FSLogix Edit** option (The VHD(X) must not be in use otherwise it cannot be mounted). This will mount both the container into windows explorer and the registry hive contained with the container

[![Context Menu Mount command]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/FRXContextMount.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/FRXContextMount.png)

An alternate way to mount the VHDX files is to use Disk Management and click on Action and Attach VHD. Make sure the VHD/VHDX file is not in use

[![Standard VHD mount method]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ExplorerAttachVHD.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ExplorerAttachVHD.png)

Once this is mounted you can see it as a disk in Disk Management

[![Mounted VHD]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/DiskManagementMounted.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/DiskManagementMounted.png)

You need to assign a drive letter and path in order to access the content of the newly attached drive.

Remember to unassign the drive before mounting otherwise FSLogix will see it as a corrupt Container.

Now you can view the VHDX and access it within Windows Explorer as below: Mounted Office Container Contents

[![Explorer View of Office Container]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ExplorerOfficeContainer.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ExplorerOfficeContainer.png)

[![Windows Search Data]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ExplorerOutlook.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ExplorerOutlook.png)

Mounted VHDX viewing the Windows Search files relating to Outlook

[![Outlook based search data]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ExplorerOutlookSearch.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/ExplorerOutlookSearch.png)

### Asking Outlook to have a crack at rebuilding the index for you

This, of course, assumed that you are in a position where you can reach the index…if you cannot, then you are probably reading this article

[![Rebuild index via Outlook]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/Indexing.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/Indexing.png)

Some things to be aware of around resetting the search indexes:

-  The search feature will not work temporarily, while index rebuilding operation is in the process. It is recommended not to interfere with the application during the process.
-  The time duration it takes to rebuild indexes solely depends on system specifications, the number of files to be indexed and volume of data each file incorporates.
-  Once the process to rebuild indexes is completed successfully, restart the application and check ‘Search' functionality.

## Confirming the Index

When your index is happily working and roaming, you should be in a position where your index according to outlook looks like the below (0 items to be indexed).

[![Indexing Status]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/OutlookIndexing.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/OutlookIndexing.png)

[![Happy Index]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/OutlookIndexingSuccess.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/OutlookIndexingSuccess.png)

If you are experience degradation of search or index performance as a whole, then most likely you will see that your index is constantly rebuilding. This indicates problems with roaming the index, or some sort of index corruption

## Known Issues / Scenarios that are troublesome

In the field, to date, there have been a few things that are nasty and have bitten us. A few things to watch for are

-  Redirected Search via GPO based Folder Redirections. Don't do this, it tends to be quite problematic when you have redirected search indexes via Shell Folder (Folder Redirection) and then you throw containers in the mix. If you see redirected searches, turn them off and be done with them
-  Redirected Start Menu. If you are redirecting modern Start Menus then you are already broken in one way or another. Don't do this. By default, the Start Menu is also indexed by the Windows Search service, and if it's been configured as redirected you are already behind the eight ball

Any decent profile management solution these days should be able to roam the Start Menu appropriately (let's not go too deep there though as that's a can of worms) and if you have full profile Containers then instant win. One gotcha here is that if you have previously been redirecting the start menu via GPO/Shell Folders, you cannot simply set back to "Not Configured", you need to set the policy setting to "Redirect to the local user profile location"

[![Redirecting the start menu back to local location]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/StartRedirection.png)]({{site.baseurl}}/assets/img/fslogix-containers-search-index-considerations-and-troubleshooting/StartRedirection.png)

-  It is ideal to have your FSLogix Registry/GPO settings baked into your master images. Trying to retrofit these values in non-persistent VDI or RDSH environments can prove a challenge due to how quickly the FSLogix Service starts and reads existing value. If you cannot do this, and you experience configuration problems (you say ON, FSLogix actions OFF, Environment reflects your value of ON) then you should consider restarting the FSLogix Service shortly after Start-Up as there is a good chance your image has the wrong value baked in
-  Other plug-ins can stop Search working. We have had a number of instances where a plug-in has stopped the search service. Symantec Enterprise Vault Client has been a particular problem.
-  Make sure Outlook is fully patched as there are a number of search-related fixes and optimisations in the patches
-  When checking the status of the search items indexed you will see the "items remain to be indexed" number increase to start with when first caching a mailbox as it has more to index. You will then see this figure slowly reduce but it can take several minutes to be fully indexed.

## A real-life example of troubleshooting (Gareth)

With all these things in mind, here's a real-life example of the Search indexing feature in use and how we used the above information to troubleshoot the problem effectively.

Gareth recently had a problem with Search indexing within an FSLogix solution and set about troubleshooting it with James K's help. Despite the fact that James doesn't look anywhere near as good in hot pants as Gareth's favourite Australian, Kylie Minogue, he reluctantly agreed to accept the offer of assistance 😊

Here's a rundown of what was done to resolve the issue.

### Setting the Scene

The scenario that presented itself was a bit ambiguous and unclear. What was known was that the search index appeared to be non-functional for specific users configured identically to working users. Consistently, the noisy users were complaining they had to keep rebuilding the search Index and this was negatively affecting the performance of their systems and their overall productivity.

### The Environment

The environment in question was a Citrix XenApp 7.15 LTSR CU2 build, utilising 2012 R2 VDAs and Windows Server 2016 Infrastructure Servers (WEM/Brokers/File Servers etc). FSLogix was being utilised as the profile solution utilising both profile containers for the individual users with no folder redirection, and office 365 containers used to house Office 365 data inclusive of the Search index.

### FSLogix Configuration

At the OU level, we had a single policy utilising FSLogix ADMX files configured for our XenApp VDAs.

### High-Level Troubleshooting Steps

-  Confirmed Configuration Settings via GPO
-  Confirmed Permissions on the FSLogix Shares
-  Confirmed Profile and 365 Container Operations
-  Published the `FRXTray.exe` to the troublesome user via WEM and confirmed no significant errors relating to the mounting and write operations
-  Identified some issues around Start Menu redirection – resolved these via redirecting start menu back to the original location
-  Investigated the `C:\ProgramData\FSLogix\Logs\Search Logs`, identified corrupted and problematic search catalogue mounts for the user in question
-  Attempted to repair the search index via the `frx.exe` utility – no luck
-  Disconnected User to ensure containers released
-  Mounted the VHDX containers and noted that both containers contained search index references – manually deleted all the search index files from within both containers
-  Removed any outlook search references from the Outlook folders within the containers, and took the chance to clean any duplicate OST files
-  Dismounted the VHDX containers
-  Logged The user back in, checked the Logs and can confirm that no existing index found, a new one being built for the user
-  Confirmed outlook using the existing OST file, and now rebuilding an index
-  Allowed operations to complete, logged off the user and started a new session
-  Confirmed that indexing operational and roaming OK

### Reason for Approach

If using a full container, you must remember that deleting the container will mean the user loses all contents within the container. Unless you have a full backup VHDX solution in place you should be very careful when doing this.

Some environments will redirect the documents folder outside the container to a folder redirection share that can be backed up for this purpose. My advice is always to rename the container, so you have a copy just in case.

Understanding how the index can be tracked and fixed allowed an optimal result without having to perform the traditional "Reset your profile" to fix a single issue.

## Summary

If you've configured your FSLogix Search Roaming solution correctly, then there's absolutely no reason why it shouldn't just work and keep on working.

However, as we've found out, troubleshooting the actual Search feature itself can be a bit daunting, as there are many different configurations of products and operating systems that can make use of this feature. That's why we thought it was important to put together a blog post showing the steps that we commonly take around addressing issues with Search Roaming, and also to allow administrators to better understand the technologies they are dealing with.

As with many Microsoft features, Windows Search is not greatly documented, particularly when it comes to the differences between operating system functionality. However, there is a substantial amount of developer documentation around integration with Windows Search, so the current iteration of this feature hopefully will be around in its current form, or something very close to it, for a good while yet.

Anyway, here's hoping you find this compilation of our shared experiences helpful in troubleshooting your own environments – any questions or queries, please feel free to leave a comment or hit us up on Twitter.

This post is not addressing Windows 10 Multi-Session or Windows Server 2019 Per-User Search. For changes with these Operating Systems, please see my follow up post [here](https://jkindon.com/2020/03/15/windows-search-in-server-2019-and-multi-session-windows-10/)
{:.warning}
---
layout: post
title: "Windows Search in Server 2019 and Multi-Session Windows 10"
permalink: "/windows-search-in-server-2019-and-multi-session-windows-10/"
description: Windows Search challenges in Server 2019 and Windows 10 Multi Session
categories: [Citrix, Containers, FSLogix, Outlook, Search, UPM, Windows]
redirect_from: 
    - /2020/03/15/windows-search-in-server-2019-and-multi-session-windows-10
    - /2020/03/15/windows-search-in-server-2019-and-multi-session-windows-10/
#Image: "/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/Search.jpg"
image:
  path: /assets/img/windows-search-in-server-2019-and-multi-session-windows-10/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

As always in our industry, small changes in one platform can result in significant impact across what is "standard practice" in many other areas. This week's lucky contender is Windows Search.

**Update 25/10/2021:** There is a [new update](https://support.microsoft.com/en-us/topic/october-19-2021-kb5006744-os-build-17763-2268-preview-e043a8a3-901b-4190-bb6b-f5a4137411c0) from Microsoft available stating the fix as follows:
<br><br>
*Addresses an issue that causes `searchindexer.exe` to keep handles to the per user search database in the path below after you sign out:*
<br><br>
`C:\Users\username\AppData\Roaming\Microsoft\Search\Data\Applications\{SID}`
<br><br>
*As a result, `searchindexer.exe` stops working and duplicate profile names are created.*
{:.special_note}

Windows Search in both Windows Server 2019 and Windows 10 Multi-Session has [changed how it operates](https://social.msdn.microsoft.com/Forums/en-US/a9b5000d-e2a8-442b-9cbf-48e05136f732/support-for-server-2019-and-office-2019-search-roaming?forum=FSLogix), introducing the concept of per-user search natively into the Search Service. This is fundamentally different from previous versions (namely Windows Server 2016 etc) and changes how we need to think about search roaming with supporting technologies such as FSLogix Containers and Citrix UPM.

The biggest change is that the Windows Search index is now stored *per user* in the user profile, specifically
> `C:\Users\%Username%\AppData\Roaming\Microsoft\Search\Data\Applications\{UserSID}\{UserSID}.edb`

On each login, the Windows Search process creates a new instance of the search database for the user based on the existing EDB. If no EDB file exists, a new one is created by the Operating System.

[![User Based Search Index]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/UserSearch.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/UserSearch.png)

There are two components associated with the Search functionality split into individual processes:

-  **SearchIndexer.exe**: This process runs under the `NT AUTHORITY\SYSTEM` account and accesses the index database for each user (profile based)
-  **SearchProtocolHost.exe**: an instance of this process runs per user and is a child process of **SearchIndexer.exe**. This process handles amongst other things, the indexing of the Outlook OST file, not the searching of the index file, but the actual index creation of it making content available to search. Searching should be operational with or without this process running. When new items need to be indexed, the process will run, once finished, it will stop

The impacts here, should you have dealt with FSLogix or Citrix UPM Search roaming should start to become obvious when you think about how these solutions tackle the Search side of things (they rip out a component of the index and store them within a container). The long and short of it is as follows:

-  For FSLogix environments, you must **_<u>NOT</u>_** enable search roaming within the FSLogix GPO. Specifically, you want SearchRoam set to **0** in the registry. This needs to be reversed in your master image should you have enabled it previously, in fact, given the what we know of Search challenges and the attempted hooks into it, I would be tempted to suggest that should you have enabled search roaming in either Windows Server 2019 or Windows 10 multi-session previously, then revert the setting, reinstall Microsoft Office and make sure that the event logs are clear from a Windows Search perspective
-  For Citrix UPM environments, the following article has been released which is [quite confusing](https://support.citrix.com/article/CTX270433). TLDR, the key it’s discussing should not be set to anything other than 0, and ideally shouldn’t exist as a whole in Windows Server 2019\. Citrix UPM configurations will need to cater for the new location of the Search Index, however, the OST container can stay in place and do its thing. I haven’t had time to test, but I would assume that the new search location in the user profile is an ideal candidate for the UPM container feature

Unfortunately, Windows Search is an ongoing challenge and there is a fair number of customers who are experiencing issues with the native multi-user search capability in both Windows 10 Multi-Session and Windows Server 2019. Some are experiencing repeated crashing of the service others are finding that search index files are not released on logoff resulting in locked files and corrupt indexes.

In the below walkthrough, I will try and outline what happens with Windows Search in Server 2019, and where things fall apart, along with a temporary resolution which may help until the problems are properly addressed by Microsoft.

First of all, an outline of the environment I am using to demonstrate the situation

-  Windows Server 2019 Datacenter, latest rollup at the time of writing
-  FSLogix release 2.9.7237.48865
-  Profile and Office Container configured
-  Microsoft Office 365 ProPlus, x64, Semi-Annual channel
-  No PVS or MCS provisioning

Secondly, below is an outline of some of the event log entries we will be referring to:

| **ID** | **Detail** |
| --- | :-- |
| **5** | Windows Search Service has created default configuration for new user `KINDO\JKindon5` |
| **2** | SearchIndexer (4400,P,98) {`S-1-5-21-2397015974-2202110191-2245630456-1134`}: The database engine (10.00.17763.0000) is starting a new instance (3) |
| **102** | SearchIndexer (4400,P,98) {`S-1-5-21-2397015974-2202110191-2245630456-1134`}: The database engine (10.00.17763.0000) is starting a new instance (3) |
| **105** | SearchIndexer (4400,D,0) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The database engine started a new instance (3). (Time=0 seconds) |
| **637** | SearchIndexer (4400,D,35) `S-1-5-21-2397015974-2202110191-2245630456-1134`: New flush map file `C:\Users\JKindon5\AppData\Roaming\Microsoft\Search\Data\Applications\{S-1-5-21-2397015974-2202110191-2245630456-1134}\{S-1-5-21-2397015974-2202110191-2245630456-1134}.jfm` will be created to enable persisted lost flush detection |
| **325** | SearchIndexer (4400,D,35) {`S-1-5-21-2397015974-2202110191-2245630456-1134`}: The database engine created a new database (6, `C:\Users\JKindon5\AppData\Roaming\Microsoft\Search\Data\Applications\{S-1-5-21-2397015974-2202110191-2245630456-1134}\{S-1-5-21-2397015974-2202110191-2245630456-1134}.edb`). (Time=0 seconds) |
| **103** | SearchIndexer (6672,T,97) {`S-1-5-21-2397015974-2202110191-2245630456-1134`}: The database engine stopped the instance (3) |
| **2** | Unable to remove Windows Search Service indexed data for user 'KINDO\JKindon5' in response to user profile deletion. Error code 0x80004002 |
| **482** | SearchIndexer (3768,D,0) `S-1-5-21-2397015974-2202110191-2245630456-1134`: An attempt to write to the file `C:\Users\JKindon5\AppData\Roaming\Microsoft\Search\Data\Applications\S-1-5-21-2397015974-2202110191-2245630456-1134\S-1-5-21-2397015974-2202110191-2245630456-1134.jfm` at offset 0 (0x0000000000000000) for 8192 (0x00002000) bytes failed after 0.000 seconds with system error 21 (0x00000015): "The device is not ready.". The write operation will fail with error -1022 (0xfffffc02). If this error persists then the file may be damaged and may need to be restored from a previous backup |
| **492** | SearchIndexer (9200,D,0) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The logfile sequence in `C:\Users\JKindon5\AppData\Roaming\Microsoft\Search\Data\Applications\S-1-5-21-2397015974-2202110191-2245630456-1134\` has been halted due to a fatal error.  No further updates are possible for the databases that use this logfile sequence.  Please correct the problem and restart or restore from backup |
| **439** | SearchIndexer (3768,T,97) `S-1-5-21-2397015974-2202110191-2245630456-1134`: Unable to write a shadowed header for file |`C:\Users\JKindon5\AppData\Roaming\Microsoft\Search\Data\Applications\S-1-5-21-2397015974-2202110191-2245630456-1134\S-1-5-21-2397015974-2202110191-2245630456-1134.edb`. Error -1022 |
| **104** | SearchIndexer (3768,T,97) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The database engine stopped the instance (2) with error (-1022) |
| **103** | SearchIndexer (3768,T,97) Windows: The database engine stopped the instance (0) |
| **300** | SearchIndexer (10076,R,98) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The database engine is initiating recovery steps. |
| **301** | SearchIndexer (10076,R,98) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The database engine has finished replaying logfile `C:\Users\JKindon5\AppData\Roaming\Microsoft\Search\Data\Applications\S-1-5-21-2397015974-2202110191-2245630456-1134\edb.jtx` |
| **302** | SearchIndexer (10076,U,98) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The database engine has successfully completed recovery steps |
| **326** | SearchIndexer (9808,D,50) `S-1-5-21-2397015974-2202110191-2245630456-1104`: The database engine attached a database (2, `C:\Users\JKindon\AppData\Roaming\Microsoft\Search\Data\Applications\S-1-5-21-2397015974-2202110191-2245630456-1104\S-1-5-21-2397015974-2202110191-2245630456-1104.edb`). (Time=0 seconds) |

My findings here are an aggregation of existing posts, as well as feedback from colleagues in the World of EUC slack channel. Testing has been validated in my own environments.

## First Logon for the user

On the first logon for a new user, the following event logs entries will occur, basically detecting a new user without an index, creating a new database, configuring and leveraging the new instance as below:

**Event ID 5:** Windows Search Service has created the default configuration for new user `Kindo\JKindon5`

[![EventID5]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID5.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID5.png)

**Event ID 102:** SearchIndexer (4400,P,98) {`S-1-5-21-2397015974-2202110191-2245630456-1134`}: The database engine (10.00.17763.0000) is starting a new instance (3)

[![EventID102]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID102.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID102.png)

**Event ID 105:** SearchIndexer (4400,D,0) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The database engine started a new instance (3). (Time=0 seconds)

[![EventID105]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID105.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID105.png)

**Event ID 637:** SearchIndexer (4400,D,35) `S-1-5-21-2397015974-2202110191-2245630456-1134`: New flush map file `C:\Users\JKindon5\AppData\Roaming\Microsoft\Search\Data\Applications\{S-1-5-21-2397015974-2202110191-2245630456-1134}\{S-1-5-21-2397015974-2202110191-2245630456-1134}`.jfm will be created to enable persisted lost flush detection

[![EventID637]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID637.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID637.png)

**Event ID 325**: SearchIndexer (4400,D,35) {`S-1-5-21-2397015974-2202110191-2245630456-1134`}: The database engine created a new database (6, `C:\Users\JKindon5\AppData\Roaming\Microsoft\Search\Data\Applications\{S-1-5-21-2397015974-2202110191-2245630456-1134}\{S-1-5-21-2397015974-2202110191-2245630456-1134`}.edb`). (Time=0 seconds)

[![EventID325]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID325.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID325.png)

In summary, in a healthy operational state, for a new user, the following event log patterns will occur on logon:

-  5 = Search Creates a default configuration
-  102 = Search Indexer creates a new instance for the user
-  105 = Search Indexer starts the new instance for the user
-  637 = New flush map file created for the user
-  325 = Search Indexer creates the new database

## User Logoff

When the user logs off for the first time, the following event entry is logged:

**Event ID 103:** Event 103: SearchIndexer (6672,T,97) {`S-1-5-21-2397015974-2202110191-2245630456-1134`}: The database engine stopped the instance (3)

[![EventID103]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID103.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID103.png)

And this is where things go pear-shaped, you will most probably find the 103 entry is quickly followed by this one:

**Event ID 2:** Unable to remove Windows Search Service indexed data for user `KINDO\JKindon5` in response to user profile deletion. Error code 0x80004002

[![EventID2]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID2.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/EventID2.png)

In this state, when the user next connects, they are going to be hit with a problem. What we are looking for, is a happy combination of the following events when the user logs on for the second time:

-  102 = Search Indexer creates a new instance for the user
-  105 = Search Indexer starts the new instance for the user
-  326 = Search Indexer attaches an existing database

What we end up with though due to the above event ID 2 on Logoff, is the following:

**Event ID 482:** SearchIndexer (3768,D,0) `S-1-5-21-2397015974-2202110191-2245630456-1134`: An attempt to write to the file `C:\Users\JKindon5\AppData\Roaming\Microsoft\Search\Data\Applications\S-1-5-21-2397015974-2202110191-2245630456-1134\S-1-5-21-2397015974-2202110191-2245630456-1134.jfm` at offset 0 (0x0000000000000000) for 8192 (0x00002000) bytes failed after 0.000 seconds with system error 21 (0x00000015): The device is not ready. The write operation will fail with error -1022 (0xfffffc02). If this error persists then the file may be damaged and may need to be restored from a previous backup.

The root cause of this is due to the following handles still being kept open by the **SearchIndexer.exe** process

[![Performance Monitor locked files]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/SearchIndexerProcess.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/SearchIndexerProcess.png)

These files are held open; however, the physical files have gone (detached) and thus we loop.

[![Locked files with no access]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/OpenFiles.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/OpenFiles.png)

If the user was to log back on at this point, we will experience some problematic symptoms and events:

Outlook indexing will present as below:

[![Failed Outlook Index]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/IndexingStatus.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/IndexingStatus.png)

And no files in the `%AppData%\Microsoft\Search\Data\Applications\{UserSID}`` directories will be touched

## Restarting the search service

If we restart the Windows Search Service, a few things happen:

All existing handles are closed for SearchIndexer operations, both for any logged off users in a fail state, and for any other user on the Server, and the following event logs are written

| **ID** | **Detail** |
| --- | :-- |
| **439** | SearchIndexer (3768,T,97) `S-1-5-21-2397015974-2202110191-2245630456-1134`: Unable to write a shadowed header for file `C:\Users\JKindon5\AppData\Roaming\Microsoft\Search\Data\Applications\S-1-5-21-2397015974-2202110191-2245630456-1134\S-1-5-21-2397015974-2202110191-2245630456-1134.edb`. Error -1022. |
| **104** | SearchIndexer (3768,T,97) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The database engine stopped the instance (2) with error (-1022) |
| **103** | SearchIndexer (3768,T,97) Windows: The database engine stopped the instance (0) |
| **102,105,326** | (happy combination representing success) |
| **300** | SearchIndexer (10076,R,98) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The database engine is initiating recovery steps |
| **301** | SearchIndexer (10076,R,98) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The database engine has finished replaying logfile |
| **302** | SearchIndexer (10076,U,98) `S-1-5-21-2397015974-2202110191-2245630456-1134`: The database engine has successfully completed recovery steps |
| **105,326** | (Search index is OK again) |

Outlook indexing is now OK

[![Outlook Indexing in Action]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/IndexingStatusHealthy.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/IndexingStatusHealthy.png)

Along with successful alterations to the `%AppData%\Microsoft\Search\Data\Applications\{UserSID}`` directories.

Fixed it would seem. So, logically here, we know that the resolution is to restart the Windows Search Service based on the failure Event, which is represented by **Event ID 2\.** Specifically:

-  **Event Log:** Application
-  **Event Level:** Error
-  **Event Source:** Search-ProfileNotify
-  **Event ID:** 2
-  **Event Data:** Unable to remove Windows Search Service indexed data for user `KINDO\JKindon5` in response to user profile deletion. Error code 0x80004002

By implementing a scheduled task with the above Event ID as the trigger, and a simple configuration of PowerShell to restart the search service as the action:

[![Scheduled Task - General Properties]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/SchedTasks.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/SchedTasks.png)

[![Scheduled Task - Trigger Properties]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/SchedTasksTrigger.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/SchedTasksTrigger.png)

[![Scheduled Task - Action Properties]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/SchedTasksProgram.png)]({{site.baseurl}}/assets/img/windows-search-in-server-2019-and-multi-session-windows-10/SchedTasksProgram.png)

We can successfully clear the errors on log-off, release the files, and ensure that when the user logs back in, their index is good to go. The process will now look like the below when a user logs off:

| **ID** | **Detail** |
| --- | :-- |
| **103** | for the user logging off |
| **2** | This triggers the restart of the service, which also triggers an **Event ID: 103** for all other users on the Server |
| **102, 105, 326** | for all remaining user accounts on the server |

Based on testing so far, this process does not hurt user experience, inclusive of existing Outlook sessions with existing search indexes, however, your mileage will vary, and this is at the end of the day, simply a workaround for a problem that needs to be fixed.

## Summary

There are multiple streams of issues associated with native multi-user search currently, a [running an interesting stream is located here](https://social.msdn.microsoft.com/Forums/en-US/a9b5000d-e2a8-442b-9cbf-48e05136f732/support-for-server-2019-and-office-2019-search-roaming?forum=FSLogix) on the Microsoft forums which pointed to the end resolution of a scheduled restart of the Search Service on the specific Event ID trigger, however, there have been multiple discussions around the World of EUC slack channel with people willing to test and share results – namely [Kasper Johansen](https://twitter.com/KasperMJohansen), [Mike Streetz](https://twitter.com/O_P), [Dennis Mohrmann](https://twitter.com/mohrpheus78)
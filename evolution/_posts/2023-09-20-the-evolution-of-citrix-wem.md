---
layout: post
title: The Evolution of Citrix Workspace Environment Management
permalink: "/the-evolution-of-citrix-wem/"
categories: [Citrix, UPM, Profiles, Windows, CVAD, Cloud, evolution]
description: Tracking the progress of Citrix Workspace Environment Management capability On-Prem
image:
  path: /assets/img/the-evolution-of-citrix-wem/post_default_image.jpg
sitemap: true
hide_last_modified: false
comments: true
related_posts:
  - evolution/_posts/2023-09-20-the-evolution-of-citrix-profile-management.md
  - evolution/_posts/2023-09-20-the-evolution-of-citrix-machine-creation-services-with-microsoft-azure.md
  - evolution/_posts/2023-09-20-the-evolution-of-citrix-wem-service.md
---

<!--excerpt-->

-  Table of Contents
{:toc .large-only}

Citrix WEM is consistently updating and bringing enhanced capability to the table. This post aims to track the changes and releases as they occur, and provide a single point of reference. I have started the tracking at 1912, anything before that, read the docs.

I will do my best to maintain this list as and when features come out, as well as some commentary around their value where I can.

## Version 2308

### [Web console (Preview)](https://docs.citrix.com/en-us/workspace-environment-management/current-release/install-and-configure/web-console)

A new web-based Workspace Environment Management (WEM) console is now available. Citrix have completed the process of migrating the critical features from the legacy console to the web console. The web console generally responds faster than the legacy console. The following features are available only in the web console.

-  Scripted tasks
-  Triggers
-  Configure Windows GPOs by using Group Policy Administrative Templates
-  Insights
-  Save a backup of a configuration set automatically and more.

### [Enhanced WEM agent event logging](https://docs.citrix.com/en-us/workspace-environment-management/current-release/reference/agent-event-logs)

Citrix have made enhancements to WEM agent event logging, aiming at improving troubleshooting capabilities. The enhancements include:

-  Comprehensive event logs: We have provided comprehensive event logs, giving you a complete picture of agent activities.
-  Unique event IDs: Each event log now has a distinct ID, making it easier for you to filter and identify specific events.

### [Microsoft Edge browser support for WEM Transformer](https://docs.citrix.com/en-us/workspace-environment-management/current-release/whats-new.html#microsoft-edge-browser-support-for-wem-transformer)

The WEM Transformer now supports the latest version of the Microsoft Edge browser.

### [Enhancements to CPU spike protection](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/system-optimization/cpu-management#cpu-spike-protection-option)

This release introduces enhancements to the CPU spike protection feature, giving you more granular control. The enhancements include the following changes:

-  Citrix have reorganized CPU spike protection options with intuitive logic for easier configuration, which applies only to the WEM Web console.
-  When customizing CPU spike protection, you can now configure the CPU usage limit using non-integer values. This configuration applies to both legacy and the web console.
-  A new option, **Set limit relative to single CPU core**, is now available and applicable to the web console, letting you set a limit on CPU usage based on a single CPU core as a reference.

### [WiX support for WEM AdminConsole and Infrastructure installers](https://docs.citrix.com/en-us/workspace-environment-management/current-release/whats-new.html#wix-support-for-wem-adminconsole-and-infrastructure-installers)

Citrix is offering WiX-based installers for the infrastructure services and administration console. The new installers include changes to the .Net framework, installation path, service name, event log name, parameters, and more.

Watch out. The install and service names have changed. This means any form of startup scripts for cache-management etc. will need to be updated
{:.attention}

### [Dynamic token support for Group Policy settings](https://docs.citrix.com/en-us/workspace-environment-management/current-release/reference/dynamic-tokens#dynamic-token-support-for-group-policy-settings)

You can now use dynamic tokens in **Group Policy** settings. This feature allows for more adaptable policy configuration in different environments, reduces manual configuration, and simplifies policy management.

See also [WEM Variables, Dynamic Tokens, Hashtags and Strings](https://jkindon.com/wem-variables-dynamic-tokens-hashtags-and-strings/)

## Version 2305

### [Enhancement to gMSA support](https://docs.citrix.com/en-us/workspace-environment-management/current-release/install-and-configure/infrastructure-services.html#group-managed-service-account)

This enhancement simplifies the process of configuring a group Managed Service Account (gMSA) for use with Workspace Environment Management (WEM). You can now use the GUI to configure the account. After binding the Citrix WEM SPN with the account, you can select the account in the same way as you do for an AD user when you do the following:

-  [Create a WEM database](https://docs.citrix.com/en-us/workspace-environment-management/current-release/install-and-configure/infrastructure-services.html#create-a-workspace-environment-management-database)
-  [Configure the infrastructure service](https://docs.citrix.com/en-us/workspace-environment-management/current-release/install-and-configure/infrastructure-services.html#configure-the-infrastructure-service)
-  [Upgrade the database](https://docs.citrix.com/en-us/workspace-environment-management/current-release/upgrade.html#step-2-upgrade-the-database)

### [Wake up agents](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/advanced-settings.html#wake-on-lan)

This release introduces the Wake on LAN feature, which lets you remotely turn on agent hosts. WEM automatically selects agents that reside on the same subnet as the target agents and uses those agents as Wake on LAN messengers. This feature requires hardware compatible with Wake on LAN. To use this feature, verify that the target machines satisfy the hardware requirements and relevant BIOS settings are configured.

### [Profile Management Settings](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/policies-and-profiles/citrix-upm-settings.html#main-citrix-profile-management-settings)

Workspace Environment Management now supports the following Profile Management policies. The following new option is now available in the **Administration Console > Policies and Profiles > Citrix Profile Management Settings.**

-  **Enable active write back on session lock and disconnection**. Available on the **Main Profile Management Settings** tab. If enabled, profile files and folders are written back only when a session is locked or disconnected. With both this option and the **Enable Active write back registry** option enabled, registry entries are written back only when a session is locked or disconnected.
-  **Enable VHD disk compaction**. Available on the **Profile Container Settings** tab. If enabled, VHD disks are automatically compacted on user logoff when certain conditions are met. This option enables you to save the storage space consumed by profile container, OneDrive container, and mirror folder container. Advanced options are available on the **Advanced Settings** tab, including **Disable defragmentation for VHD disk compaction, Set free space ratio to trigger VHD disk compaction**, and **Set number of logoffs to trigger VHD disk compaction**. When **Enable VHD disk compaction** is enabled, use these three options to adjust the default VHD compaction settings and behavior.
-  **Enable asynchronous processing for user Group Policy on logon**. Available on the **Advanced Settings** tab. If enabled, Profile Management roams with users a registry value that Windows uses to determine the processing mode for the next user logon — synchronous or asynchronous processing mode. This ensures that the actual processing mode is applied each time users log on.
-  **Enable app access control**. Available on the **App Access Control** tab. If enabled, Profile Management controls user access to items (such as files, folders, and registries) based on the rules you provide. A typical use case is to apply rules to control user access to apps installed on machines — whether to make apps invisible to relevant users. This feature can simplify application and image management. For example, using the feature, you can deliver identical machines to different departments while meeting their different application needs, thus reducing the number of images.

## Version 2303

### [Profile Management](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/policies-and-profiles/citrix-upm-settings.html)

Workspace Environment Management now supports all versions of Profile Management through 2303. The following new options are now available in **Administration Console > Policies and Profiles > Citrix Profile Management Settings**.

-  **Enable concurrent session support**. Provides native Outlook search experience in concurrent sessions. If enabled, each concurrent session uses a separate Outlook OST file. You can specify the maximum number of VHDX disks for storing Outlook OST files. The option is available in **Policies and Profiles > Citrix Profile Management Settings > Advanced Settings**.
-  **Enable profile streaming for pending area**. If enabled, files in the pending area are fetched to the local profile only when they are requested. This ensures optimum logon experience in concurrent session scenarios. The option is available in **Policies and Profiles > Citrix Profile Management Settings > Streamed User Profiles**.

## Version 2212

### [Apply settings to unbound agents](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/active-directory-objects.html#advanced)

Starting with this release, you can apply settings to agents that are not bound to any configuration set. This feature lets you control how unbound agents behave

### [Profile Management](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/policies-and-profiles/citrix-upm-settings.html#file-deduplication)

Workspace Environment Management now supports all versions of Profile Management through `2212`. The following tab is now available in **Administration Console > Policies and Profiles > Citrix Profile Management Settings**.

-  **File Deduplication**. If enabled, Profile Management removes duplicate files from the user store and stores one copy of them in a central location. Doing so reduces the load on the user store by avoiding file duplication, thus reducing your storage cost

### [Change to Google Analytics configuration](https://docs.citrix.com/en-us/workspace-environment-management/current-release/install-and-configure/infrastructure-services.html#configure-the-infrastructure-service)

Starting with this release, Workspace Environment Management determines which option of the following two to select based on the region of the machine hosting the infrastructure service:

-  **Help improve Workspace Environment Management using Google Analytics**. This option is selected if the machine resides in non-European regions
-  **Do not help improve Workspace Environment Management using Google Analytics**. This option is selected if the machine resides in European regions

This behavior applies only to fresh installations.

## Version 2209

### [Ability to import Group Policy settings from registry files](https://docs.citrix.com/en-us/workspace-environment-management/2209/user-interface-description/actions/group-policy-settings.html#import-group-policy-settings-from-registry-files)

Starting with this release, you can convert registry values that you export using the Windows Registry Editor into GPOs for management and assignment. If you are familiar with the **Import registry files** option available with **Registry Entries**, this feature:

-  Lets you import registry values under both `HKEY_LOCAL_MACHINE` and `HKEY_CURRENT_USER`
-  Lets you import registry values of the `REG_BINARY` and `REG_MULTI_SZ` types
-  Supports converting delete operations associated with registry keys and values that you define in .reg files

### [External task](https://docs.citrix.com/en-us/workspace-environment-management/2209/user-interface-description/actions/external-tasks.html)

This release includes enhancements to the external task feature. The feature now provides you with three additional options to control when to run external tasks:

-  **Disconnect**. Controls whether to run the external task when a user disconnects from a machine where the agent is running
-  **Lock**. Controls whether to run the external task when a user locks a machine where the agent is running
-  **Unlock**. Controls whether to run the external task when a user unlocks a machine where the agent is running

### [Profile Management](https://docs.citrix.com/en-us/workspace-environment-management/2209/user-interface-description/policies-and-profiles/citrix-upm-settings.html#advanced-settings)

Workspace Environment Management now supports all versions of Profile Management through `2209`. The following new option is now available in the **Administration Console > Policies and Profiles > Citrix Profile Management Settings > Advanced Settings** interface.

-  **Enable OneDrive container**. If enabled, Profile Management roams OneDrive folders with users by storing the folders on a VHDX disk. The disk is attached during logons and detached during logoffs

## Version 2206

### [Process hierarchy control](https://docs.citrix.com/en-us/workspace-environment-management/2206/user-interface-description/security.html#process-hierarchy-control)

This release introduces the process hierarchy control feature. The feature lets you control whether certain child processes can be started through their parent processes. You create a rule by defining parent processes and then designating an allow list or a block list for their child processes. You then assign the rule on a per user or per user group basis. The following rule types are available:

-  Path. Applies the rule to an executable according to the executable file path
-  Publisher. Applies the rule according to publisher information
-  Hash. Applies the rule to identical executables as specified

For the feature to work, you need to use the **AppInfoViewer** tool on each agent machine to enable the feature. Every time you use the tool to enable or disable the feature, a machine restart is required

### [Enhancements to memory management](https://docs.citrix.com/en-us/workspace-environment-management/2206/user-interface-description/system-optimization/memory-management.html)

This release includes enhancements to the memory management feature. The feature now provides you with two extra options to perform memory management:

-  **Do Not Optimize When Total Available Memory Exceeds (MB).** This option lets you specify a threshold below which WEM optimizes memory usage for idle applications
-  **Enable Memory Usage Limit for Specific Processes.** This option lets you limit the memory usage of processes by setting upper limits for the memory they can consume

### [Administration console](https://docs.citrix.com/en-us/workspace-environment-management/2206/whats-new.html#whats-new-in-2206)

The user interface of the administration console has changed:

-  A new node, **Process Hierarchy Control**, is now available in **Security**. The node contains a tab that lets you control whether certain child processes can be started through their parent processes
-  An option, **Do Not Optimize When Total Available Memory Exceeds**, is now available in **System Optimization > Memory Management > Memory Management**. The option lets you specify a threshold limit below which Workspace Environment Management optimizes memory usage for idle applications
-  A new tab, **Memory Usage Limit**, is now available in **System Optimization > Memory Management**. The tab lets you configure memory usage limits for specific processes

## Version 2203

### [Allow users to self-elevate certain applications](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/security.html#self-elevation)

This release introduces self-elevation for the privilege elevation feature. With self-elevation, you can automate privilege elevation for certain users without the need to provide the exact executables beforehand. Those users can request self-elevation for any applicable file simply by right-clicking the file and then selecting Run with administrator privileges in the context menu. All actions are audited based on requested input from the user

### [Configure user processes as triggers for external tasks](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/actions/external-tasks.html#external-task-list)

Two additional options to control when to run external tasks have been added

-  **Run when processes start** controls whether to run the external task when specified processes start
-  **Run when processes end** controls whether to run the external task when specified processes end

This allows for specific actions to apply when a specific process is triggered or ended - very cool

### [Profile container insights](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/monitoring.html#profile-container-insights)

a long awaited addition to the on-premises release, you can monitor profile containers for Profile Management and FSLogix. The feature provides insights into the basic usage data of the profile containers, the status of sessions using the profile containers, the issues detected, and more. Use the feature to stay on top of space usage for profile containers and to identify problems that prevent profile containers from working

### [Administration console Updates](https://docs.citrix.com/en-us/workspace-environment-management/current-release/whats-new.html)

The administration console user interface has changed:

In **Security**, there is a new node, **Self-elevation**. The node contains a tab that lets you automate privilege elevation for users.

In **Monitoring**, there is a new node, **Profile Container Insights**. The node contains two tabs. The **Summary** tab includes two pie charts, providing a summary that shows the status of profile containers. The Profile Container Status tab displays a list of status records for profile containers

## Version 2112

### [Privilege elevation](https://docs.citrix.com/en-us/workspace-environment-management/2112/user-interface-description/security.html#privilege-elevation)

a feature from the Cloud Service, this release introduces the privilege elevation feature. The feature lets you elevate the privileges of non-administrative users to an administrator level necessary for some executables. As a result, those users can start those executables as if they are members of the administrators group.

You can configure privilege elevation using two types of rules: executable rules and Windows installer rules. You can configure how a rule behaves according to the type of the operating system. You can also configure whether a rule takes effect at a particular point in time or within a time range. You assign a rule on a per user or per user group basis

### [Support for optimizing multi-session OS machines](https://docs.citrix.com/en-us/workspace-environment-management/2112/user-interface-description/system-optimization/multi-session-optimization.html)

With this feature, you can optimize multi-session OS machines where disconnected sessions are present. The feature improves the user experience of connected sessions by limiting the number of resources disconnected sessions can consume.

This is similar to how ControlUp etc offer some process stripping functionality for disconnected sessions. This is also less harsh on the page file etc as optmization is only performed on disconnected sessions

### [Administration console Updates](https://docs.citrix.com/en-us/workspace-environment-management/2112/whats-new.html)

The administration console user interface has changed:

-  In **System Optimization**, there is a new **Multi-session Optimization** node. On the node, there is a new **Multi-session Optimization** tab for you to configure settings designed to optimize multi-session OS machines with disconnected sessions
-  In **Security**, there is a new **Privilege Elevation** node. On the node, there is a **Privilege Elevation** tab for controlling whether to enable the feature and to apply global settings. Below the node, there are two subnodes:
    -  **Executable Rules** with a **Privilege Elevation** tab where you can apply privilege elevation using executable rules
    -  **Windows Installer** Rules with a **Privilege Elevation** tab where you can apply privilege elevation using Windows installer rules

## Version 2109

### [Support for Windows 11](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/2109/whats-new/windows-11.html)

The Citrix components, features, and technologies in this release that support the Windows 10 OS now also support Windows 11, unless otherwise noted

### [Support for running Workspace Environment Management in FIPS mode](https://docs.citrix.com/en-us/workspace-environment-management/2109/reference/fips-mode.html)

You can now run Workspace Environment Management (WEM) in a FIPS environment

## Version 2106

### [Windows Server 2022 support](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/2106/whats-new/windows-server-2022.html)

The Citrix components and technologies in this release that support Windows Server platforms now also support Windows Server 2022, unless otherwise noted

### [Overwrite or merge application security rules](https://docs.citrix.com/en-us/workspace-environment-management/2106/user-interface-description/security.html#application-security)

This release adds two settings, **Overwrite** and **Merge**, to the **Administration Console > Security > Application Security** tab. The settings let you determine how the agent processes application security rules.

-  Select **Overwrite** if you want to overwrite existing rules. When selected, the rules that are processed last overwrite rules that were processed earlier. Citrix recommend that you apply this setting only to single-session machines.
-  Select **Merge** if you want to merge rules with existing rules. When conflicts occur, the rules that are processed last overwrite rules that were processed earlier.

Nice to not have to go and edit the SQL database directly for this one

### [Support for the Windows 10 2009 template in Citrix Optimizer](https://docs.citrix.com/en-us/workspace-environment-management/2106/user-interface-description/system-optimization/citrix-optimizer.html)

You can now use Workspace Environment Management to perform template-based system optimizations for Windows 10 2009 machines. In addition, Citrix have updated all existing templates to reflect changes introduced in the latest standalone Citrix optimizer

## Version 2103

### [Profile Management](https://docs.citrix.com/en-us/workspace-environment-management/2103/user-interface-description/policies-and-profiles/citrix-upm-settings.html)

Workspace Environment Management now supports all versions of Profile Management through 2103. Also, the following new options are now available in the **Administration Console > Policies and Profiles > Citrix Profile Management Settings** interface:

-  **Enable Local Cache for Profile Container**
    -  Available on the P**rofile Container Settings** tab
    -  If enabled, each local profile serves as a local cache of its profile container
-  **Enable multi-session write-back for profile containers**
    -  Available on the **Advanced Settings** tab
    -  Replaces **Enable multi-session write-back for FSLogix Profile Container** of previous releases to accommodate multi-session write-back support for Citrix Profile Management profile containers
-  **Enable Profile Streaming for Folders**
    -  Available on the Streamed User Profiles tab
    -  If enabled, folders are fetched only when they are being accessed

### [SDK documentation](https://developer-docs.citrix.com/projects/workspace-environment-management-sdk/en/latest/)

This release updates PowerShell modules in the Workspace Environment Management SDK. The following cmdlets are no longer usable:

-  Property SDKInfrastructureServiceConfiguration.AgentSyncPort
-  Property Commandlets.SetWemInfrastructureServiceConfiguration.AgentSyncPort

Version 2103 of the Workspace Environment Management SDK documentation reflects the update.

## Version 2012

### [WEM agent integration with the Citrix Virtual Apps and Desktops product software](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/2012/whats-new.html#citrix-virtual-apps-and-desktops-7-2012)

The WEM agent is integrated with the Citrix Virtual Apps and Desktops product software, letting you include the WEM agent when installing a Virtual Delivery Agent (VDA). This integration is reflected in the Citrix Virtual Apps and Desktops 2012 product software and later. For more information, see Citrix Virtual Apps and Desktops 7 2012.

If you enable the **Workspace Environment Management** check box on the **Additional Components** page, the **WEM Server** page appears. That page is titled **WEM Infrastructure Server**. Enter the FQDN or IP address of the WEM infrastructure server. Then click **Add**. The WEM agent on the VDA communicates with that infrastructure server.

### [Optimized WEM agent startup](https://docs.citrix.com/en-us/workspace-environment-management/2012/install-and-configure/agent-host.html#agent-startup-behaviors)

Previously, the WEM agent startup workflow had the following issues:

-  The agent did not refresh the Citrix Cloud Connector settings after startup. As a result, the Cloud Connector settings deployed to the agent through group policies did not work as expected
-  In a non-persistent environment, when the agent cache file resided in the base image, the agent could experience cache synchronization issues. As a result, WEM settings might not be applied properly

Starting with this release, the agent refreshes Cloud Connector settings after startup, just like it refreshes other settings. To ensure that the agent cache is up to date, the agent automatically recreates the cache in non-persistent environments

### [New agent cache utility options](https://docs.citrix.com/en-us/workspace-environment-management/2012/install-and-configure/agent-host.html#agent-cache-utility-options)

This release adds the following agent cache utility options:

-  `-RefreshSettings` or `-S`: Refreshes agent host settings
-  `-Reinitialize` or `-I`: Reinitializes the agent cache when used together with the -RefreshCache option

### [Citrix optimizer](https://docs.citrix.com/en-us/workspace-environment-management/2012/user-interface-description/system-optimization/citrix-optimizer.html)

Citrix optimizer now provides an additional option that enables WEM to automatically select templates for your OS:

-  **Automatically Select Templates to Use**. If you are unsure which template to use, use this option to let WEM select the best match for each OS. You can also apply this option to custom templates with different name formats by using the **Enable Automatic Selection of Templates Starting with Prefixes** option

### [Support for the Windows 10 2004 template](https://docs.citrix.com/en-us/workspace-environment-management/2012/user-interface-description/system-optimization/citrix-optimizer.html)

WEM adds support for the Windows 10 2004 template introduced in Citrix optimizer. You can now use WEM to perform template-based system optimizations for Windows 10 2004 machines

### [Support for editing Group Policy settings](https://docs.citrix.com/en-us/workspace-environment-management/2012/user-interface-description/actions/group-policy-settings.html#edit-group-policy-settings)

Previously, you could change only the name and description for a GPO after importing your GPO settings. You can now edit registry operations associated with a GPO. You can also add new registry operations to a GPO if needed. Currently, WEM supports adding and editing only Group Policy settings that are associated with the `HKEY_LOCAL_MACHINE` and the `HKEY_CURRENT_USER` registry hives.

When editing Group Policy settings, you have the following actions: **Set value, Delete value, Create key, Delete key**, and **Delete all values**

### [Multiple selection support for action groups](https://docs.citrix.com/en-us/workspace-environment-management/2012/user-interface-description/actions/action-groups.html)

Previously, when adding actions to an action group, you moved each action present in the **Available** pane to the **Configured** pane one by one. You can now move multiple actions in a single step

### [WEM agent (advanced notice) of change](https://docs.citrix.com/en-us/workspace-environment-management/2012/whats-new.html)

Microsoft Sync Framework 2.1 will reach End of Life on January 12, 2021. WEM will retire the associated legacy agent cache sync service and switch to using the latest agent cache sync service to keep the agent cache in sync with the infrastructure services. The latest agent cache sync service relies on Dotmim.Sync, an open-source sync framework. How does this change impact you?

-  If you use Workspace Environment Management 1912 or later, this change does not require action on your part
-  If you use Workspace Environment Management 1909 or earlier, upgrade to Workspace Environment Management 1912 or later

This change is scheduled to be rolled out in March 2021

## Version 2009

### [Support for the Windows 10 1909 template](https://docs.citrix.com/en-us/workspace-environment-management/2009/user-interface-description/system-optimization/citrix-optimizer.html)

WEM adds support for the Windows 10 1909 template introduced in Citrix optimizer. You can now use WEM to perform template-based system optimizations for Windows 10 1909 machines

### [Profile Management Additions](https://docs.citrix.com/en-us/workspace-environment-management/2009/user-interface-description/policies-and-profiles/citrix-upm-settings.html#profile-container-settings)

Workspace Environment Management now supports all versions of Profile Management through 2009. The following new options are now available on the Administration Console > Policies and Profiles > Citrix Profile Management Settings > Profile Container Settings tab:

-  **Enable Folder Exclusions for Profile Container** (option for excluding the listed folders from the profile container)
-  **Enable Folder Inclusions for Profile Container** (option for keeping the listed folders in the profile container when their parent folders are excluded)

### [Administration console changes](https://docs.citrix.com/en-us/workspace-environment-management/2009/whats-new.html)

The administration console user interface has changed:

-  In **Administration Console > Policies and Profiles > Citrix Profile Management Settings**, there is a new **Profile Container Settings** tab for you to configure Profile Management profile container settings
-  The **Enable Profile Container** option now moves to the **Profile Container Settings** tab. Previously, the option was present on the **Synchronization** tab

## Version 2006

### [Enhancements to Group Policy Object (GPO) migration](https://docs.citrix.com/en-us/workspace-environment-management/2006/user-interface-description/actions/group-policy-settings.html)

This release makes further enhancements to GPO migration. Different from the **Migrate** wizard, which lets you migrate only Group Policy Preferences (GPP), you can now also import Group Policy settings (registry-based settings) into WEM. After importing the settings, you can have an itemized view of the settings associated with each GPO before you decide which GPO to assign. You can assign the GPO to different AD groups. To import Group Policy settings, navigate to **Administration Console > Actions > Group Policy Settings**, select **Enable Group Policy Settings Processing**, and then click **Import** to open the import wizard

### [Administration console changes](https://docs.citrix.com/en-us/workspace-environment-management/2006/whats-new.html)

The administration console user interface has changed:

-  In **Actions**, there is a new **Group Policy Settings** pane. In the pane, there is a **Group Policy Settings** tab for you to configure Group Policy settings

## Version 2003

### [Citrix optimizer](https://docs.citrix.com/en-us/workspace-environment-management/2003/user-interface-description/system-optimization/citrix-optimizer.html)

Citrix optimizer is now available in Workspace Environment Management (WEM). You can use the feature to optimize user environments for better performance. Citrix optimizer runs a quick scan of user environments and then applies template-based optimization recommendations. You can optimize user environments in two ways:

-  You can use built-in templates to perform optimizations. To do so, select a template applicable to the operating system
-  Alternatively, you can create your own custom templates with specific optimizations you want and then add them to WEM

### [External task](https://docs.citrix.com/en-us/workspace-environment-management/2003/user-interface-description/actions/external-tasks.html)

This release includes enhancements to the external task feature. The feature now provides you with two additional options to control when to run external tasks:

-  **Logoff**. This option lets you specify whether to run external tasks when users log off
-  **Reconnect**. This option lets you specify whether to run external tasks when a user reconnects to a machine on which the agent is running. This option is not applicable to scenarios where the WEM agent is installed on a physical Windows device

The logoff option can be useful in scenarios where you want to purge the user environment on logoff

### [Optimized action processing](https://docs.citrix.com/en-us/workspace-environment-management/2003/user-interface-description/advanced-settings.html#agent-options)

tarting with this release, WEM supports processing actions without retrieving settings from the infrastructure services. There is a new "**Use Cache to Accelerate Actions Processing**" option on the **Administration Console > Advanced Settings > Configuration > Agent Options** tab. The option enables the WEM agent to process actions by using the agent local cache. As a result, the agent no longer needs to communicate with the infrastructure services when processing actions

### [Optimized logon performance](https://docs.citrix.com/en-us/workspace-environment-management/2003/install-and-configure/agent-host.html#system-settings)

In earlier releases, WEM delayed user logons until the processing of user Group Policy settings completed. Starting with this release, WEM no longer delays logons, and user Group Policy settings are processed in the background by default.

### [Optimized file type associations](https://docs.citrix.com/en-us/workspace-environment-management/2003/user-interface-description/actions/file-associations.html)

In previous releases, file type associations other than those for text (.txt) files did not work consistently. Starting with this release, file type associations that you configure become default associations automatically. This enhancement lets you more effectively manage user environments. In addition, you now have more flexibility in configuring file type associations. In the **New File Association** window, you no longer have to fill out the following fields: **Action, Target application**, and **Command**. You can leave the fields empty as long as you can provide the correct **ProgID**

### [Profile Management updates](https://docs.citrix.com/en-us/workspace-environment-management/2003/whats-new.html)

As of this release, you can use the Workspace Environment Management to configure all settings for Citrix Profile Management 2003. The following option is now available in the administration console:

-  **Enable multi-session write-back for FSLogix Profile Container** (option to save changes in multi-session scenarios for FSLogix Profile Container)

### [Administration console](https://docs.citrix.com/en-us/workspace-environment-management/2003/whats-new.html)

The user interface of the administration console has changed:

-  In **System Optimization**, there is a new **Citrix Optimizer** pane. In the pane, there is a **Citrix Optimizer** tab for configuring optimization-related settings.

## Version 1912

### [Replacing Microsoft SQL Server Compact (SQL CE) with SQLite](https://docs.citrix.com/en-us/workspace-environment-management/1912/whats-new.html)

The Workspace Environment Management (WEM) agent can work in offline mode. In earlier releases, the agent relied on Microsoft SQL Server Compact to synchronize with SQL Server to facilitate offline mode. Microsoft SQL Server Compact 3.5 Service Pack 2 is the last version that supports this functionality. Versions 4.0 and later do not support synchronization with SQL Server. However, SQL Server Compact 3.5 Service Pack 2 reached End of Life (EOL) in 2018. Starting with this release, the agent relies on SQLite for offline mode to work.

### [Support for exporting and importing configuration sets](https://docs.citrix.com/en-us/workspace-environment-management/1912/user-interface-description/ribbon.html)

Starting with this release, WEM supports exporting and importing configuration sets using the administration console. To export configuration sets, use the **Backup** wizard, where the **Configuration set** option is available on the **Select what to back up** page. To import configuration sets, use the **Restore** wizard, where the **Configuration set** option is available on the **Select what to restore** page. You can export and import only one configuration set at a time

### [Option to reset actions](https://docs.citrix.com/en-us/workspace-environment-management/1912/user-interface-description/advanced-settings.html#ui-agent-personalization)

Starting with this release, WEM supports resetting assigned actions (purging action-related registry entries in the user environment). The feature also provides the flexibility to reset assigned actions. You can reset all assigned actions by using the administration console or let users decide what to reset in their environment. The feature might be useful in scenarios where actions you assign to users or user groups do not take effect

[Sound familiar?](https://jkindon.com/selective-deletion-of-the-wem-actions-tracking-cache/)

### [Administration console changes](https://docs.citrix.com/en-us/workspace-environment-management/1912/whats-new.html)

The administration console user interface has changed:

-  The **Advanced Settings > UI Agent Personalization > UI Agent Options** tab introduces an "**Allow Users to Reset Actions**" option. Use that option to control whether to let current users specify what actions to reset in their environment

### [Agent administrative templates](https://docs.citrix.com/en-us/workspace-environment-management/1912/install-and-configure/agent-host.html#step-1-configure-group-policies-optional)

There are now two policies associated with the WEM agent cache synchronization:

-  **Cache synchronization port** (Applicable to Workspace Environment Management 1909 and earlier; replaced by **Cached data synchronization port** in Workspace Environment Management 1912 and later. The port defaults to 8285.)
-  **Cached data synchronization port** (Applicable to Workspace Environment Management 1912 and later; replaces **Cache synchronization port** of Workspace Environment Management 1909 and earlier. The port defaults to 8288.)

Starting with this release, the WEM agent relies on **Cached data synchronization port** to keep the agent cache in sync with the WEM infrastructure service. If you have Workspace Environment Management 1909 or earlier deployed in your environment, you cannot not use **Cached data synchronization port**. Instead, use **Cache synchronization port**

### [Upgrade enhancement](https://docs.citrix.com/en-us/workspace-environment-management/1912/upgrade.html)

This release simplifies the process of upgrading the WEM database. In earlier releases, to upgrade the database, you needed to remove the database from the availability group if the database was deployed in a SQL Server Always On availability group. Starting with this release, you can upgrade the database without removing it from the availability group.

Note that you still need to back up the database before you perform the upgrade

### [Workspace Environment Management (WEM) PowerShell SDK modules](https://docs.citrix.com/en-us/workspace-environment-management/1912/whats-new.html)

This release includes enhancements to the PowerShell modules in the WEM SDK. You can now use the PowerShell SDK to:

-  Create, update, query, and delete configuration sets and user-level and machine-level AD objects
-  Export and import configuration sets or user-level or machine-level AD objects

---
layout: post
title: "Whats new in CVAD 1906"
permalink: "/whats-new-in-cvad-1906/"
description: "New stuff in CVAD 1906"
categories: [Apps and Desktops, Citrix, UPM, WEM, XenApp, XenServer]
redirect_from: 
    - /2019/06/18/whats-new-in-cvad-1906
    - /2019/06/18/whats-new-in-cvad-1906/
image:
  path: /assets/img/whats-new-in-cvad-1906/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

A list of new features associated with the latest Citrix Virtual Apps and Desktops 1906 release. This is a direct copy from the Citrix Documentation in an aggregated view.

Release Date: June 17, 2019

## Citrix Virtual Apps and Desktops 7 1906

This Citrix Virtual Apps and Desktops release includes new versions of the Windows Virtual Delivery Agents (VDAs) and new versions of several core components.

### Universal Print Server: Server component

In earlier releases, when installing the UPS server component on your Windows print server, ports 7229 and 8080 were opened in the Windows firewall automatically. Now, you can change the default ports using the graphical interface by default. You can change that default setting. You can change that default setting. For details, see [Install core components](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure/install-core.html#next-steps).

When using the command-line interface, you must include the `/enable_upsserver_port` option to open those ports. For details, see [Install using the command line](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure/install-command.html#install-the-universal-print-server).

Also, the Microsoft Visual C++ 2017 runtime is installed. The Visual C++ 2013 and 2015 runtimes are no longer installed.

### Removal of Smart Tools agent

When installing a Delivery Controller or VDA, the Smart Tools agent is no longer installed.

-  In the graphical interface, the **Smart Tools**page is now titled **Diagnostics**.
-  In the command-line interface, `/exclude "Smart Tools Agent"` is no longer valid when installing a Delivery Controller or a VDA. The `/includeadditional "Smart Tools Agent"` option is no longer valid when installing a VDA.

### Citrix Scout: Health checks and version check

You can now use Citrix Scout to run health checks on your Delivery Controllers and VDAs.

Also, when using the **Collect** or **Trace & Reproduce** features in Scout, a check is made to determine whether the Delivery Controllers and VDAs are running the latest version of the Citrix Telemetry Service. You are alerted if an earlier version is found, so that you can install the current version.

For details, see [Citrix Scout](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/manage-deployment/cis/scout.html).

## Citrix Director

### Session Auto Reconnect

The Sessions page on the Trends tab now includes information about the number of auto reconnects. Auto reconnects are attempted when the Session Reliability or Auto Client Reconnect policies are in effect. The auto reconnect information helps you view and troubleshoot network connections having interruptions, and also analyze networks having a seamless experience. 

The drilldown provides additional information like Session Reliability or Auto Client Reconnect, time stamps, Endpoint IP, and Endpoint Name of the machine where the Workspace app is installed. This feature is available for Citrix Workspace app for Windows, Citrix Workspace app for Mac, Citrix Receiver for Windows, and Citrix Receiver for Mac. This feature requires Delivery Controller version 7 1906 or later, and VDAs 1906 or later. For more information, see:

-  [Sessions](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/manage-deployment/sessions.html)
-  [Auto client reconnect policy settings](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/policies/reference/ica-policy-settings/auto-client-reconnect-policy-settings.html)
-  [Session reliability policy settings](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/policies/reference/ica-policy-settings/session-reliability-policy-settings.html)
-  [Session Auto Reconnect](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/director/site-analytics/trends.html#available-trends)

### Session startup duration

Director now displays the session startup duration divided into Workspace App Session Startup and VDA Session Startup time periods. This data helps you to understand and troubleshoot high session startup duration. Further, the time duration for each phase involved in the session startup helps in troubleshooting issues associated with individual phases. For example, if the Drive Mapping time is high, you can check if all the valid drives are mapped properly in the GPO or script. This feature is available on Delivery Controller version 7 1906 or later and VDAs 1903 or later. For more information, see [Diagnose session startup issues](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/director/troubleshoot-deployments/user-issues/session-startup.html).

### Desktop probing

This feature automates health checks of virtual desktops published in a Site, thereby improving the user experience. To initiate desktop probing, install and configure the Citrix Probe Agent on one or more endpoint machines. Desktop probing is available for Premium licensed Sites. This feature requires Delivery Controller(s) version 7 1906 or later and Citrix Probe Agent 1906 or later. For more information, see [Desktop Probing](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/director/troubleshoot-deployments/applications/desktop-probing.html).

> **Note**: Citrix Probe Agent now supports TLS 1.2.

## Virtual Delivery Agents (VDAs) 1906

Version 1906 of the VDA for Windows Server OS and the VDA for Windows Desktop OS includes the following enhancements (in addition to the VDA installation and upgrade items listed above):

### Legacy TWAIN enhancements

We made significant enhancements to the overall code quality to bring more stability and reliability to the legacy TWAIN feature.

### New time zone control policy setting

The Restore desktop OS time zone on session disconnect or logoff policy setting specifies the time zone behavior when a user disconnects or logs off from a single session. For more information, see [Restore desktop OS time zone on session disconnect or logoff](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/policies/reference/ica-policy-settings/time-zone-control-policy-settings.html#restore-desktop-os-time-zone-on-session-disconnect-or-logoff).

### Optimization for Microsoft Teams

> **NOTE**: This feature depends on a future Microsoft Teams release. We will update this description as information about the version and release date become available.

We added optimization for desktop-based Microsoft Teams using Citrix Virtual Apps and Desktops and Citrix Workspace app. Optimization for Microsoft Teams is similar to HDX RealTime Optimization for Microsoft Skype for Business. The difference is, we bundle all necessary components for optimization for Microsoft Teams into the VDA and the Workspace app for Windows. For more information, see [Optimization for Microsoft Teams](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/multimedia/opt-ms-teams.html) and [Policies](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/policies/reference/ica-policy-settings/multimedia-policy-settings.html#microsoft-teams-redirection).

### Support for Local Security Authority (LSA) protection

We now support the use of Local Security Authority (LSA) protection on a single-session desktop OS. In Windows, you can configure extra protection for the LSA process to increase the security for the credentials that it stores and manages.

### Globalization

The Windows VDAs are localized in Dutch. For globalization information, see [CTX119253](https://support.citrix.com/article/ctx119253).

## Citrix Licensing 11.15

Citrix Licensing 11.15 contains [new features](https://docs.citrix.com/en-us/licensing/current-release/about.html) and [fixed](https://docs.citrix.com/en-us/licensing/current-release/about/fixed-issues.html) and [known](https://docs.citrix.com/en-us/licensing/current-release/about/known-issues.html) issues.

## Citrix Federated Authentication Service PowerShell cmdlets

Several Federated Authentication Service (FAS) PowerShell cmdlets have been added in this release. These allow you to perform diagnostics on the FAS, and on any Certificate Authorities or hardware security modules (HSMs) the FAS uses. The new cmdlets are:

-  Test-FasCertificateSigningRequest
-  Test-FasCrypto
-  Test-FasKeyPairCreation
-  Test-FasUserCertificateCrypto
-  Get-FasPrivateKeyPoolInfo

For more information, see [Federated Authentication Service cmdlets Reference](https://developer-docs.citrix.com/projects/federated-authentication-service-powershell-cmdlets/en/latest/).

## StoreFront 1906

StoreFront 1906 includes the following new features.

### StoreFront Protocol Handler Support now includes Linux

When users on supported Linux platforms open a Citrix Receiver for Web site, and Citrix Workspace App for Linux 1903 or higher is installed, the browser automatically opens ICA files using Citrix Workspace App for Linux at launch. The client detection work flow for Linux, which determines whether Citrix Workspace App for Linux is installed, is now identical to Citrix Workspace App for Windows and Citrix Workspace App for MAC clients when Chrome and FireFox browsers are used. In previous releases, users on Linux were required to manually open a downloaded ICA file first.

### Citrix Analytics service

You can now configure Citrix StoreFront so that Citrix Workspace App can send data to the Citrix Analytics service. Citrix Analytics aggregates metrics on users, applications, endpoints, networks, and data to provide comprehensive insights into user behavior. To enable this feature, import a configuration file from the Citrix Analytics service into a StoreFront server or server group using new PowerShell cmdlets. This enables all stores to communicate with the Citrix Analytics service. Configuration details are described in [Citrix Analytics service](https://docs.citrix.com/en-us/storefront/current-release/install-standard.html#citrix-analytics-service). PowerShell details are provided in [Citrix StoreFront 1906 SDK PowerShell Modules](https://developer-docs.citrix.com/projects/storefront-powershell-sdk/en/latest/). This functionality is supported for the following scenarios:

-  Stores which are accessed by browsing to Citrix Receiver for Web sites in HTML5-compatible browsers. CAS data is supplied when launching resources using either the native Citrix Workspace app or using HTML5.
-  Stores which are accessed from Citrix Workspace app 1903 for Windows or later.
-  Stores which are accessed from Citrix Workspace app 1901 for Linux or later.

### StoreFront SDK PowerShell Modules updated

The Citrix StoreFront SDK PowerShell Modules are updated and republished as version 1906\. Changes include new PowerShell cmdlets to support the Citrix Analytics service. See [Citrix StoreFront 1906 SDK PowerShell Modules](https://developer-docs.citrix.com/projects/storefront-powershell-sdk/en/latest/).

## Citrix Provisioning 1906

This release of Citrix Provisioning includes a new wizard in the provisioning console used to export devices to the Citrix Virtual Apps and Desktops Delivery Controller. It also contains support for Citrix Hypervisor 8.0 and introduces accelerated Microsoft office activation. Numerous fixes and enhancements are also part of this release. See the [fixed](https://docs.citrix.com/en-us/provisioning/current-release/fixed-issues.html) and [known](https://docs.citrix.com/en-us/provisioning/current-release/known-issues.html) issues for additional information about this release of Citrix Provisioning.

### Citrix Provisioning devices export wizard

The devices export wizard exports existing provisioned devices to the Citrix Virtual Apps and Desktops Delivery Controller. This wizard augments the existing import functionality from Studio’s machine creation wizard in Citrix Cloud implementations. For more information, see [Using the Export Devices Wizard](https://docs.citrix.com/en-us/provisioning/current-release/configure/export-devices-wizard.html). 

> **NOTE:** The devices export wizard is the preferred method for adding existing devices in your Citrix Provisioning farm to a Citrix Virtual Apps and Desktops Delivery Controller.

### Support for Citrix Hypervisor 8.0

This release provides experimental support for UEFI virtual machine provisioning on Citrix Hypervisor 8.0. Citrix Hypervisor is the complete server virtualization platform from Citrix, you can use to create and manage a deployment of virtual x86 computers running on Xen. See the [Citrix Hypervisor](https://docs.citrix.com/en-us/citrix-hypervisor.html) documentation for more information. Consider the following when using this functionality:

-  Windows guest operating system UEFI boot on a provisioned virtual machine is an experimental feature. For more information, see [Experimental features](https://docs.citrix.com/en-us/citrix-hypervisor/whats-new/experimental.html).
-  Known limitations include:
    -  UEFI VM with NVRAM.
    -  Citrix Provisioning Accelerator with UEFI VM on Citrix Hypervisor 8.0.

### Accelerated Microsoft office activation

With this release of Citrix Provisioning, an administrator can force the immediate activation of a Microsoft Office license once a system starts up. In previous releases, Key Management Service (KMS) Microsoft Office activation did not complete before a user logs in to the VM. This delay resulted in users encountering licensing warning messages, which led users to believe that they were not using a licensed version of Microsoft Office.

## Workspace Environment Management 1906

Workspace Environment Management 1906 includes the following new features:

### Action Groups

Action Groups functionality has been added to the administration console **Actions** pane. This functionality lets you configure a group of actions that you want to assign to a user or user group. For more information, see [Action Groups](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/actions/action-groups.html).

### Administration console

The administration console user interface has changed:

-  In **Actions**, there is a new **Action Groups** In the **Actions > Action Groups**pane, there is a new **Action Group List** tab for configuring a group of actions that you want to assign to a user or user group.
-  An "Enable Notifications" option is provided on the **Advanced Settings > Configuration > Agent Options** You can use this option to control whether the agent displays notification messages on the agent host when the connection to the infrastructure service is lost or restored.

### Upgrade enhancement

This release includes the following enhancements to improve the user experience with the upgrade of Workspace Environment Management:

-  After upgrading the infrastructure service, the fields on all the tabs of **WEM Infrastructure Service Configuration**are automatically populated with the data you previously configured. As a result, you no longer need to type the required information manually after you upgrade the infrastructure service.
-  After upgrading the database, the fields in the **Database Upgrade Wizard**window are automatically populated with the data you previously configured. As a result, you no longer need to type the required information manually after you upgrade the database.

### Profile management

Workspace Environment Management now supports all versions of Profile Management through 1903\. The following new option is now available on the **Administration Console > Policies and Profiles > Citrix Profile Management Settings > Synchronization** tab:

-  Enable Profile Container (option for eliminating the need to save a copy of the folders to the local profile)

### VMware Persona settings deprecation

Support for VMware Persona settings has been deprecated. All VMware Persona settings content will be removed from the documentation in the next release. For more information, see [Deprecation](https://docs.citrix.com/en-us/workspace-environment-management/current-release/deprecation.html).

## Profile Management 1906

his version includes the following enhancements and addresses several issues to improve the user experience.<

### Enable native Outlook search experience

The Enable native Outlook search experience feature now supports the Microsoft Outlook 2016 64-bit edition.

## HDX RealTime Optimization Pack 2.8 (existing)

### Enhanced audio quality

Enhanced audio quality because of the improved echo cancellation on Windows.

### Remote connectivity

Support for Skype for Business calls when the Edge server is not reachable. In these cases, the Optimization Pack goes into fallback mode and audio and video processing occurs on the server.

## Session Recording 1906

This release includes the following new features and enhancements:

### Web browsing activities can be logged

Previously available as an experimental feature, logging of web browsing activities is now fully supported. You can use this feature to log user activities on supported browsers and tag the events in the recording. For more information, see [Log events](https://docs.citrix.com/en-us/session-recording/current-release/log-events.html) and [Event logging policies](https://docs.citrix.com/en-us/session-recording/current-release/configure/policies.html#event-logging-policies).

### Recording viewing policies

You can now use the Session Recording Policy Console to create recording viewing policies and add multiple rules to each policy. Each rule determines which user or user group can view the recordings originating from other users and user groups you specify. For more information, see [Configure policies](https://docs.citrix.com/en-us/session-recording/current-release/configure/policies.html#recording-viewing-policies).

## AppDNA 1906

AppDNA 1906 does not include any new features. For information about bug fixes, see [Fixed issues](https://docs.citrix.com/en-us/dna/current-release/whats-new/fixed-issues.html).

## Deprecated Features Announcements

The following shows the platforms, Citrix products, and features that are deprecated or removed. _Deprecated_ items are not removed immediately. Citrix continues to support them in this release but they will be removed in a future Current Release:

### Core server components on Windows Server 2012 R2 (including Service Packs). Includes: Delivery Controller, Studio and Director.

Deprecation announced in: 1906
{:.warning}

Removed in: TBD

Alternative: Install on a supported operating system.

### Support for Site Configuration, Configuration Logging, and Monitoring databases on Microsoft SQL Server versions 2008 R2, 2012, and 2014 (including all Service Packs and editions).

Deprecation announced in: 1906
{:.warning}

Removed in: TBD

Alternative: Install databases on a supported Microsoft SQL Server version.

### Support for VDAs on Windows 10 on x86 platforms.

Deprecation announced in: 1906
{:.warning}

Removed in: TBD

Alternative: Install VDAs on a supported x64 operating system.

## Deprecated Features

-  Removal of Citrix Smart Tools Agent from Citrix Virtual Apps and Desktops installation media.
-  Support for Citrix Smart Scale in all Citrix Virtual Apps and Desktops (and XenApp and XenDesktop) versions. This functionality will reach End of Life on 31 May 2019.
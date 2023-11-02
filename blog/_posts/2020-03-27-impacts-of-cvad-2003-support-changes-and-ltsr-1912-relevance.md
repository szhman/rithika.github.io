---
layout: post
title: "Impacts of CVAD 2003 support changes and LTSR 1912 relevance"
permalink: "/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/"
description: Understanding the impact of Support changes in CVAD 2003 and the new relevance of LTSR when thinking about Public Cloud
categories: [Apps and Desktops, Azure, Citrix, Cloud, WEM, Windows, Windows 10, Workspace, XenApp]
redirect_from: 
    - /2020/03/27/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance
    - /2020/03/27/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/
image:
  path: /assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

A significant number of changes on support stances are being introduced with Citrix Virtual Apps and Desktops 2003 Current Release. These changes will directly impact the supportability of many environments and need to be considered when scoping or performing any form of upgrade/implementation works. The most impactful change is the new stances on public cloud workloads. Details can be found [here](https://support.citrix.com/article/CTX270373). Key takeaways are below:

-  A site running on Citrix Virtual Apps and Desktops 2003 or higher with workloads in public clouds will be an unsupported configuration
-  Customers running Citrix Virtual Apps and Desktops service in Citrix Cloud and customers following the Long Term Service Release (LTSR) deployment track are not impacted by this announcement. Citrix Virtual Apps and Desktops 1912 LTSR will continue to support public cloud workloads
-  Customers who typically upgrade to the latest Current Release and leverage cloud workloads have two options:
    -  Remain on the Citrix Virtual Apps and Desktops 1912 release, which has full support for public cloud providers.  As an LTSR release, 1912 will not receive new feature updates, but has a support lifecycle of 5+ years and is regularly updated with security patches and fixes through Cumulative Updates
    -  Migrate to Citrix Virtual Apps and Desktops service. The Citrix Virtual Apps and Desktops service offers full support for public cloud and on-premises workloads and the latest integration enhancements
-  There is no technical enforcement in Citrix Virtual Apps and Desktops 2003; however, a Citrix Virtual Apps and Desktops 2003 site with public cloud workloads will be treated as an unsupported configuration.  A future Current Release will enforce this change
-  To ensure continuity, Citrix recommended that customers with workloads in public clouds do not upgrade to CVAD 2003 and instead remain on CVAD 1912 or move to the Citrix Virtual Apps and Desktops service
-  When product enforcement is in place, it will focus on VDA workloads and does not impact controllers. A customer may run their Citrix Virtual Apps and Desktops infrastructure in public clouds and connect to on-prem workloads if they choose

A summary of Cloud Platform Support Changes is below:

-  1912 LTSR will be the last release supported on Public Cloud Providers from a VDA perspective IF you are leveraging a traditional Citrix deployment on-premises or on-cloud
-  You can choose to run Current Release based infrastructure components on-cloud should you so wish, but your VDA workloads must not run on-cloud past the 1912 LTSR release. These workloads must live on-premises
-  If you are using Citrix Cloud, Current Release VDA components will be supported on public Cloud as per current support models

## Supported Hypervisor/Platform

The below tables outline the supported stance for 2003 and 1912 LTSR. Sources:

-  [https://support.citrix.com/article/CTX270373](https://support.citrix.com/article/CTX270373)
-  [https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/1912-ltsr.html](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/1912-ltsr.html)
-  [https://docs.citrix.com/en-us/citrix-virtual-apps-desktops](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops)
-  [https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service)

The following table summaries the support for Hypervisor/Platforms:

[![Supported Hypervisor and Platform]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/SupportedHypervisor.png)]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/SupportedHypervisor.png)

## Active Directory Support

There are no changes on the support or requirements associated with Active Directory:

[![AD Support]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/ADSupport.png)]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/ADSupport.png)

## Database Support CVAD 2003

You might need to upgrade your SQL Server version before you can upgrade your Citrix components. Some key takeaways below:

-  Site databases: SQL Server 2008 R2, 2012, and 2014 are **no longer supported** for the site database (This includes the monitor and configuration logging databases.)
-  Local host cache database: SQL Server version 2014 **is no longer supported** for the local host cache database
-  For upgrades, any existing SQL Server Express version is **<u>not</u>** upgraded
-  Microsoft SQL Server Express LocalDB 2017 with Cumulative Update 16 is installed for use with the Local Host Cache feature. This installation is separate from the default SQL Server Express installation for the site database. (When upgrading a Controller, the existing Microsoft SQL Server Express LocalDB version is **<u>not</u>** upgraded
-  Citrix Provisioning 2003 adds support for Microsoft SQL Server 2019

A summary table below outlines database supported stance for 2003 and 1912 LTSR:

[![Database Support]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/Database.png)]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/Database.png)

## Operating System Support

Windows Server 2012 R2: Delivery Controllers, Studio, Director, VDAs, or the Universal Print Server are no longer supported for clean install or upgrades A summary table below outlines database supported stance for 2003 and 1912 LTSR:

[![OS Support 1]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/OSList1.png)]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/OSList1.png)

[![OS Support 2]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/OSList2.png)]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/OSList2.png)

## Misc

-  Donet Framework 4.8 will now be the minimum required version for Citrix components with a dotnet requirement – time to update task sequences and/or reference images with the dotnet components prior to installing Citrix components
-  PVD was deprecated a long time ago, it will now be removed from the GUI entirely, interestingly Personal vDisk functionality is still supported when using earlier VDAs with Citrix Virtual Apps and Desktops 7 2003 Delivery Controllers
-  If you have been running command line installers, the /baseimage command line switch will break your deployments
-  Installing the Microsoft Visual C++ 2017 Runtime on a machine that has the Microsoft Visual C++ 2015 Runtime installed can result in automatic removal of the Visual C++ 2015 Runtime. This is as designed.
-  Citrix License Server 11.16 and later is supported
-  AppDNA 7.8 and AppDNA 7.9 are not supported with this release

## Deprecations and Feature Removal

-  Self Service Password Reset (SSPR)
-  Azure and AWS public cloud (including VMware Cloud on AWS) connections (removed also)
-  Citrix Indirect Display Driver (IDD) graphics adapter support on Windows 10 version 1709 and earlier (removed also)
-  Hardware encoding with Nvidia GPUs (NVENC) using GRID 9 or earlier display drivers (removed also)
-  Support for Microsoft .NET Framework versions prior to version 4.8 for VDAs and core server components. Includes Delivery Controller, Studio, Director, and StoreFront.
-  VDAs on Windows Server 2012 R2
-  AppDNA application migration component of Citrix Virtual Apps and Desktops Premium edition
-  Installing Studio on 32-bit (x86) machines
-  Core server components on Windows Server 2012 R2 (including Service Packs). Includes: Delivery Controller, Studio and Director
-  Support for Site Configuration, Configuration Logging, and Monitoring databases on Microsoft SQL Server versions 2008 R2, 2012, and 2014 (including all Service Packs and editions)

## Final Summary VDA Support Breakdown

The below table summarises VDA support with a focus on changes. Check product documentation for other VDA support scenarios relating to Current Release etc:

[![VDA Support Final]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/VDAFinal.png)]({{site.baseurl}}/assets/img/impacts-of-cvad-2003-support-changes-and-ltsr-1912-relevance/VDAFinal.png)
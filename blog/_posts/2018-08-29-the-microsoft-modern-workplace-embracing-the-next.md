---
layout: post
title: "The Microsoft Modern Workplace – Embracing the Next"
permalink: "/the-microsoft-modern-workplace-embracing-the-next/"
description: "Diving into the world of Microsoft Modern Workplace"
categories: [Azure, Cloud, Mobility, Modern Workplace, MWaaS, Office365, Windows, Windows 10]
redirect_from: 
    - /2018/08/29/the-microsoft-modern-workplace-embracing-the-next
    - /2018/08/29/the-microsoft-modern-workplace-embracing-the-next/
image:
  path: /assets/img/the-microsoft-modern-workplace-embracing-the-next/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

We live in an era of change at a pace completely unparalleled throughout our time on this planet. Everything we see and do is now viewed as a starting point rather than a final product or solution. Our children will grow up with capabilities that we haven't yet imagined, and we will continue to evolve at a frightening pace. One thing that will never change though, is our requirement to work and to provide for those we care about. What's exciting though, is that as our world gets smaller, we are better connected, and we have access to more advanced technological capability, the traditional way of working is now evolving into something new and so much more exciting. Our workplace is no longer a cubicle, nor is it an office if we don’t want it to be. It can be a home, a beach, a soccer field or a tennis court. It can be a school day excursion to a train yard with the kids, or it can be a man-cave with the football on in the background. The tools to collaborate and enhance productivity are right at our fingertips and as a consumer of all these tools, and a firm believer that work is not a boundary, I thought it time to write up a piece on the Microsoft based Modern Workplace.

What is the Modern Workplace? Well, at its base, the Modern Workplace is freedom from typical boundaries and lock ins. It’s a way of working, a way of managing and a way of securing your work environment, whilst removing the walls that have often been associated with these requirements. We left behind a long time ago the theory that security is bound to a premises and expanded our user bases out to multiple office locations, pop up ventures and any other number of industry defined expansions. The modern workplace is a focus on how users can work from anywhere, on any device with both the consumer and the corporate body being ensured that productivity, security and capability is not degraded. In fact, most of these requirements are better met in a modern workplace than they are in the 4-wall lock in.

The Modern Workplace comes with a sister concept, and that’s the one of [Modern Management](https://docs.microsoft.com/en-us/windows/client-management/manage-windows-10-in-your-organization-modern-management). It's all good and well for us to focus on the consumer and how the new way of working can benefit them, but there is also the teams that are still required to enable, manage, adapt, innovate and support this user base. Modern management is the somewhat letting go of traditional hammer and stone style management techniques where you would manage every single aspect of the desktop or device and is more a shift towards configuration-based management. This has its limitations and is not necessarily a fit for all environments, however it is becoming more and more common with the introduction of BYOD, CYOD and WhateverYouWannaCallIt-YourOwnDevice policies within the enterprise. Modern Management is not something that has to be adhered too. You can still provide a Modern Workplace and provide the capability that we will outline below, whilst providing traditional management if that is what you require.

I have included a link to an [architectural overview](https://photos.google.com/share/AF1QipPSM2b3u8bUqyk2TAVEUVyDXjTsBBB-S0a678dp1hm1chSKMXMRskiCGy6ZmJ8cKw/photo/AF1QipPlOZHndw8ula6dCDtldmcSoGGY-QRsGoCPkB7B?key=NVVVR0g4ZFV0bnlfNUxhczM2NnNucW80OTZGQ2dn) that I have worked on between flights, commutes and late-night brain racking to help you visualise what sits where. This is my own brain dump, so feel free to reference as you see fit.

[![Modern Workspace Solution Overview]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/ModernWorkspaceUpdated.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/ModernWorkspaceUpdated.png)

Modern Workspace Solution Overview
{:.figcaption}

Let's dig in to what makes up the Microsoft Modern Workplace at the time of writing (I feel like I need to include statement as the rate of enhancement is absurd)

## Microsoft Office 365 + Security E3

If you have haven't looked at or dealt with Office 365 by this point, then you may well have been living under a rock. Office 365 is Microsoft's productivity and collaboration set of services based primarily in the cloud with connectivity known as "hybrid" back to your traditional on-premise environments to support transition and coexistence.

[![O365]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/O365.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/O365.png)

Office 365 is the Collaboration Suite to provide you the tools to do your job
{:.figcaption}

In the context of the Modern Workplace, Office 365 provides the tools that you will live in from a collaboration standpoint, Email (Exchange Online), Video, Voice and IM (Skype for Business and Teams), Document Management and Storage (SharePoint and OneDrive) as well as a myriad of services that link and enhance these tools. Solutions such as Planner, Delve, Office Pro Plus, Flow, Sway and even a basic MDM capability. The list goes on. Office 365 is low hanging fruit these days. It's rare I talk to an organisation that isn't actively involved in migrating services to it, or at least consuming some sort of service within the Suite. Office 365 is one core component to the Modern Workplace Strategy The [Enterprise Mobility + Security](https://www.microsoft.com/en-au/cloud-platform/enterprise-mobility-security) bolt on licence is what opens up the world for your mobility and Modern Workplace Strategy. It is the licence SKU that opens up the premium features of Azure Active Directory, provides access to full MDM via Intune and enhanced data protection capability.

[![EMS]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/EMS.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/EMS.png)

Enterprise Mobility + Security Opens the door for Secured Modern Management
{:.figcaption}

It’s fair to say that I will focus primarily on the [E3 capability](https://www.microsoft.com/en-au/cloud-platform/enterprise-mobility-security-pricing) of this solution, it’s the most common licence I tend to work with.

An interesting and often less talked about option is the Microsoft 365 Licence (not be confused with office 365) which can provide you some nice value add via the bundling of Office 365, Enterprise Mobility and Security as well as your Windows 10 licencing.

Microsoft 365 comes in two flavours – [Business](https://www.microsoft.com/en-au/microsoft-365/business) and [Enterprise](https://www.microsoft.com/en-AU/microsoft-365/enterprise/home) each with their own sub SKU’s to allow you some flexibility. Conversely, the [Microsoft 365 Enterprise E5 SKU](https://www.microsoft.com/en-au/microsoft-365/compare-all-microsoft-365-plans) contains a licence for [Windows Defender Advanced Threat Protection](https://www.microsoft.com/en-us/windowsforbusiness/windows-atp), Office 365 Advanced Threat Protection, Office 365 Threat Intelligence. All very cool solutions.

## Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis) is the underlying Solution that realistically underpins everything in the Modern Workplace Space. Azure Active Directory is a rethink and redesign of the traditional Active Directory Domain Services (ADDS) that we have been dealing with day to day for 20 odd years. It is the core identity source for all things Office 365 and Microsoft Cloud Service based. It can and does integrate with your existing Active Directory, but it is a different beast and has been designed ground up for scale and with a different focus than ADDS.

[![AzureAD]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AzureAD.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AzureAD.png)

Azure Active Directory underpins all we do in the Modern Workspace
{:.figcaption}

If you have Sync'd your on-premise users to services such as Office 365, then you have already spun up [your first Azure Active Directory Instance](https://docs.microsoft.com/en-au/office365/securitycompliance/use-your-free-azure-ad-subscription-in-office-365). I say first as you may have multiple, and will no doubt interact with multiple Azure Active Directories throughout your time with in this space, be it B2B, B2C or just different requirements that lead to different Directories.

Azure Active Directory started out as 100% user focused, meaning that it was aimed to handle user identities only, and had no provision for handling computers like our traditional ADDS does. This has changed over the last few years and there has been a shift to start handling these requirements, which is what has really opened the door for Modern Management, Modern Workplace and Azure IaaS Service consumption. The two key components focused on computer management are:

-  [Azure Active Directory Domain Join](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-compare-with-azure-ad-join) (AADDJ): This is what allows for the Modern Workplace to come to life. This is the ability to perform a Domain Join for a Windows 10 device against Azure Active Directory, in a similar fashion that you would have traditionally done so in an ADDS environment.
-  [Azure Active Directory Domain Services](https://azure.microsoft.com/en-au/services/active-directory-ds/) (AADDS): This is aimed at Azure Workloads where effectively a "Domain Controller as a Service" model exists, allowing you to publish Active Directory Endpoints into an Azure vNet, allowing for a "Traditional" Domain Join of your azure-based machines, without the need to extend your entire ADDS environment into Azure

> [Azure Active Directory Premium](https://docs.microsoft.com/en-au/azure/active-directory/fundamentals/active-directory-get-started-premium) provides the basis for some pretty special and key technologies utilised in the Modern Workplace, allowing for more control for the users, and less overhead for the admin. A few of these capabilities are outlined below, and importantly, are forever expanding, maturing and having new features introduced:
{:.special_note}

-  [Self Service Password Reset](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-sspr-howitworks) (SSPR):  Self Service Password Seset basically allows for Azure AD to provide a cloud based point of password reset for your users, the service is driven via the Cloud and changes are written securely back to your on premise ADDS
-  [Conditional Access Policies](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/overview) (CA): Conditional Access is a capability of Azure Active Directory. With Conditional Access, you can implement automated access control decisions for accessing your cloud apps that are based on conditions
-  [Azure Access Panel](https://docs.microsoft.com/en-us/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)  This panel is basically the front-end aggregation point for Cloud apps and services provisioned to users via the Azure AD Premium offerings. If you have dealt with Citrix Unified Gateway, it’s a very similar concept providing your users a central landing place, similar to an app-store concept (do we still use that term?)
-  Azure Application Proxy (See section below)
-  Azure Active Directory Federation Services (See section below)

Azure Active Directory can house users from multiple sources. They can be cloud based users meaning that they have been created and exist only in the cloud, or they can be sourced from your existing ADDS Domain keeping them in Sync with your existing environment. This is controlled by a tool known as Azure Active Directory Connect, which gets its own section below.

## Azure Active Directory Connect

[Azure Active Directory Connect (AADC)](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect) is the tool in which on-prem Active Directory Domain Services (ADDS) users are synchronised to the Azure Active Directory. It is a cut down version of Microsoft's Identity Management System (MIM) and utilises all the same concepts as MIM and its predecessor FIM wrapped into a very user friendly and ever-expanding service for you to consume.

[![AADC]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AADC.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AADC.png)

Azure AD Connect Bridges On-Premise AD and Azure AD
{:.figcaption}

AADC started its life as a tool to Sync users (Remember DirSync?) but has grown and expanded over the years to do far more. It can now configure your Active Directory Federation Services (ADFS) environments, Sync Password Hashes, perform write back operations in your ADDS environment from Azure Active Directory Driven events, and even replace the requirement for ADFS to exist in your environment at all by implementing [SSO and Passthrough Authentication capability](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso), reducing your footprint and complexity when migrating to Office 365 based Services.

AADC is also the tool responsible for actioning Azure Active Directory Self Service Password reset requests, as well as write back operations for [Device](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback), User and Group writeback requirements.

## Intune

[Intune](https://www.microsoft.com/en-au/cloud-platform/microsoft-intune) is Microsoft's [Mobile Device Management (MDM)](https://docs.microsoft.com/en-us/intune/device-management) and [Mobile Application Management (MAM)](https://docs.microsoft.com/en-us/intune/app-management) tool. It is 100% cloud driven and forms an absolute key part of the Microsoft Modern Workplace. It is not just a traditional MDM system, but an aggregator of other services within the Microsoft Realm. Services such as Conditional Access, [Windows Store for Business](https://docs.microsoft.com/en-us/microsoft-store/microsoft-store-for-business-overview) Capabilities, Microsoft [AutoPilot Services](https://docs.microsoft.com/en-us/windows/deployment/windows-autopilot/windows-10-autopilot) etc. Many of Microsoft's offerings are available as standalone, and whilst Services such as Azure Active Directory underpin the majority of them, Intune is the glue that brings it all together.

[![Intune]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/Intune.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/Intune.png)

Intune is the MDM, MAM and Aggregator of Capability
{:.figcaption}

Intune is responsible for your typical MDM and MAM requirements, supporting devices such as Apple iOS, Apple Macintosh, Google Android, Google Android for Work, Samsung Knox, Windows Phone and of course, Windows 10 desktops. It provides full device management for corporate owned devices as well as Application Management and Protection Policies for BYOD and CYOD style strategies. Intune seamlessly integrates with your existing services with light weight connector services, allowing you to leverage cloud-based device management whilst still hosting many critical services such as PKI yourself, these adapters and connectors include:

-  Intune Certificate Connector: This allows you to pull and distribute user certificates from your on-premise CA to devices managed by Intune via either [PFX](https://docs.microsoft.com/en-us/intune/certficates-pfx-configure) distribution or [SCEP](https://docs.microsoft.com/en-us/intune/certificates-scep-configure). This is critical for certificate authentication-based services, which could be mail, applications or wireless networks. This is an inside out connection establishment over 443, so no inbound NAT's or reverse proxy requirements
-  Exchange Connector: The [Exchange Connector](https://docs.microsoft.com/en-us/intune/exchange-connector-install) allows you to expand Intune Policies and Conditional Access Policies out to on premises Exchange environments. It provides visibility into Active Sync based connections and allows you to wrap some controls around these services. As above, this is an inside out connection establishment

Intune integrates with all the big players from a device provisioning standpoint including;

-  [Apples device enrollment program (DEP)](https://docs.microsoft.com/en-us/intune/device-enrollment-program-enroll-ios)
-  [Samsung Knox](https://docs.microsoft.com/en-us/intune/android-samsung-knox-mobile-enroll)
-  [Android](https://docs.microsoft.com/en-us/intune/android-enroll)
-  [Android for Work](https://docs.microsoft.com/en-us/intune/android-work-profile-enroll)
-  [Microsoft Store for Business (AutoPilot) or Standard Windows Enrolment](https://docs.microsoft.com/en-us/intune/windows-enroll)

Intune is the basis for Modern Management. It can integrate back using [co-management with System Center Configuration Management (SCCM)](https://docs.microsoft.com/en-us/sccm/core/clients/manage/co-management-overview) or it can be completely standalone. It is the core driver of "Configuration Management" within the Modern Management mantra and is probably the fastest developing technology piece within the stack.  It is not a replacement for SCCM if you have a large reliance on the traditional way of doing things, however it can enhance SCCM and also provide a level of control that is sufficient for the Modern Workplace. Its ability to deploy Powershell Scripts to Windows 10 has really opened the gates to true capability.

## Azure Application Proxy

There are a significant amount of on premise web-based applications which are not going away any time soon. The ideal playing ground is that we of course federate our SaaS applications with some form of federation services (be it on prem or Azure AD Based), however some of these little applications live wholly and solely within the enterprise perimeter. Often we have utilised services like NetScaler's to reverse proxy these services to the outside world, which has worked a treat (and still does) for many customers. The Microsoft answer: [Azure Application Proxy](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/application-proxy)

[![AAP]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AAP.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AAP.png)

Azure Application Proxy - Simple and Effective
{:.figcaption}

The Azure Application Proxy (AAP) changes things up a bit, and utilising a lightweight Windows based Service, establishes an outbound connection to Azure AD, and allows you to publish Internal Applications into the "Enterprise Applications" component of Azure Active Directory. What is really cool here, is that it's an inside-out style setup, meaning that you do not need to be punching holes inbound on your firewalls to reverse proxy services. You can let the Azure Application Proxy establish the tunnel out, and from there you are home free.

[![AAP Connector]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AAP-Connector.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AAP-Connector.png)

Azure Application Proxy Connector
{:.figcaption}

My Marriot co-conspirator George Spiers has a [great write up](https://www.jgspiers.com/azure-application-proxy/) on how this all fits together. The other benefit of this solution is that Azure AD will publish all applications delivered via the Application Proxy into the "Access" portal, so now we have both internal and external applications being centralised in a one stop shop. Your on-prem applications can also be governed by Conditional Access policies, allowing you to extend cloud-based security and protection down to your on-premise applications.

AAP is also the delivery mechanism for [Hybrid cloud-based print](https://docs.microsoft.com/en-us/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-overview) capabilities, allowing your internal printers to become discoverable for your modern managed and roaming user base…pretty cool concept, horrible execution at this point. Watch this space I am sure

## Azure Active Directory Federation Services

[Active Directory Federation Services,](https://docs.microsoft.com/en-us/windows-server/identity/active-directory-federation-services) Love it or hate it (love the result hate the config?), is one of the key components to centralising identify and providing a single sign on experience for users across the multitude of SaaS and On-Prem based web applications that make up the day to day existence for most users.

Azure Active Directory (AAD) provides us yet another weapon to play with, and that is the [AAD iteration of Federation Services](https://docs.microsoft.com/en-au/azure/active-directory/manage-apps/configure-single-sign-on-portal). This service acts in the same way that traditional ADFS deployments do, however is 100% cloud based, and leverages a large selection of preconfigured SaaS based integrations directly into the platform. This allows you to consume all the benefits of ADFS, without having to deploy and manage your own environment - you can [leverage Microsoft's endpoints](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) to handle this work.

[![AzureADFS]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AzureADFS.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AzureADFS.png)

Azure ADFS based Application Delivery
{:.figcaption}

Azure Active Directory Federation Services unlocks some handy capability in the provisioning space around SaaS applications. For supported offerings, you can utilise Azure Identity Services to handle the [onboarding of accounts automatically](https://docs.microsoft.com/en-au/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) into your SaaS applications, nice piece of lifecycle management capability.

Again, like we discussed with the Azure Application Proxy, applications federated with the Azure AD Federation Services can be provisioned directly into the Access portal, and can also leverage the full conditional access capability provided by Azure Active Directory.

## AutoPilot

[Windows Autopilot](https://docs.microsoft.com/en-us/windows/deployment/windows-autopilot/windows-autopilot) is a collection of technologies used to set up and pre-configure new devices, getting them ready for productive use. In addition, you can use Windows Autopilot to reset, repurpose and recover devices.

[![AutoP]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AutoP.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AutoP.png)

Windows Autopilot Flow
{:.figcaption}

What this means to us is basically the automation of an Azure AD join based on the device being known as owned. That’s the long as short of it. So for users with devices being shipped directly to them, or in a reset scenario, if the device is known, the user is presented a setup screen which Is associated with your Azure AD Tenant. The device asks them for their credentials, then auto joins Azure AD (based on policies you define), ideally auto enrols into Intune, applies your policies and you are done.

There is a nice [mechanics](https://youtu.be/4K4hC5NchbE) video that walks you through the concept

## Web. Web. Web. What about Thick Applications and Data?

The world is moving to web-based applications. This has been in play for years now, we have all heard it, we have all witnessed it, and we have all seen it fall on its face. So some thick apps, as much as we don't want to hear it, are here to stay. It's pretty rare that we can deal with a unicorn user base that require no access back to on-premise applications, CRM, ERP, Financials, these systems are more often than not operating in a thick application stack and are core to everyday activities for many users.

In the Modern Workplace model, we have a couple of options to address this scenario and provide seamless and controlled access back to locations that house these resources

-  [Citrix Apps and Desktops (XenApp and XenDesktop)](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/): Be it cloud based or traditional on prem, Citrix App Delivery is still the most widely respected and utilised method of managing, delivering and securing applications to a roaming workforce. Citrix Application Delivery is what allowed us to truly start talking mobility and a work from anywhere mantra years ago, and their importance in this space has not waned. Conversely, whilst this is a Microsoft focused article, the [Citrix Workspace Offering](https://www.citrix.com.au/products/citrix-workspace/) can complement the Microsoft Modern Workplace and provide you with even more controls and security benefits

[![CitrixApps]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/CitrixApps.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/CitrixApps.png)

Citrix Apps and Desktops
{:.figcaption}

-  [Microsoft Remote Desktop Services](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/welcome-to-rds): The underlying platform that Citrix Apps and Desktops has backed off for years, native RDS feeds can nicely integrate into the Windows 10 environment if a VPN connection is available, else RDS gateway Services can integrate directory with the Azure Application Proxy (At the cost of UDP performance)

[![RDS]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/RDS.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/RDS.png)

Microsoft Remote Desktop Services
{:.figcaption}

-  [Microsoft Always On VPN](https://docs.microsoft.com/en-us/windows-server/remote/remote-access/vpn/always-on-vpn/always-on-vpn-technology-overview): The Microsoft revival of the Routing and Remote Access services stack to replace the Direct Access technology they had been spouting for years, has become an integral part of the deployments I have worked on with Customers consuming the Modern Workplace. The AlwaysOn VPN Solution provides us access back to not just traditional file shares (whilst we wait with baited breath to see how [Azure files pans out](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles) ), but also back to some pretty hefty applications when Citrix and RDS is not available. This is a completely integrated solution within Windows 10, meaning no additional client is required, and can be delivered seamlessly by both SCCM and Intune. It relies on a PKI infrastructure in an Active Directory Domain Services environment. Integrating AlwaysOn VPN technology with Azure Traffic Manager Profiles allow for load balancing across multiple regions and creates a highly available load balanced environment

[![VPN]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/VPN.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/VPN.png)

AlwaysOn VPN
{:.figcaption}

## Operations Management Suite and Windows Analytics

[Windows Analytics](https://docs.microsoft.com/en-au/windows/deployment/update/windows-analytics-get-started) is a set of solutions for [Microsoft Operations Management Suite](https://docs.microsoft.com/en-au/azure/monitoring/) (OMS) that provide you with extensive data about the state of devices in your deployment. There are currently three solutions which you can use singly or in any combination:

[![OMS]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/OMS.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/OMS.png)

Windows Analytics via OMS
{:.figcaption}

[Device Health](https://docs.microsoft.com/en-au/windows/deployment/update/device-health-get-started) which provides the following:

-  Identification of devices that crash frequently, and therefore might need to be rebuilt or replaced
-  Identification of device drivers that are causing device crashes, with suggestions of alternative versions of those drivers that might reduce the number of crashes
-  Notification of Windows Information Protection misconfigurations that send prompts to end users

[Update Compliance](https://docs.microsoft.com/en-au/windows/deployment/update/update-compliance-get-started) shows you the state of your devices with respect to the Windows updates so that you can ensure that they are on the most current updates as appropriate. In addition, Update Compliance provides the following:

-  An inventory of devices, including the version of Windows they are running and their update status
-  The ability to track protection and threat status for Windows Defender Antivirus-enabled devices
-  An overview of Windows Update for Business deferral configurations (Windows 10, version 1607 and later)
-  Powerful built-in log analytics to create useful custom queries
-  Cloud-connected access utilizing Windows 10 diagnostic data means no need for new complex, customized infrastructure

[Upgrade Readiness](https://docs.microsoft.com/en-au/windows/deployment/upgrade/upgrade-readiness-get-started) offers a set of tools to plan and manage the upgrade process end to end, allowing you to adopt new Windows releases more quickly. Upgrade Readiness provides:

-  A visual workflow that guides you from pilot to production
-  Detailed computer and application inventory
-  Guidance and insights into application and driver compatibility issues, with suggested fixes
-  Data-driven application rationalization tools
-  Application usage information, allowing targeted validation; workflow to track validation progress and decisions
-  Data export to commonly used software deployment tools, including System Center Configuration Manager

## Azure Storage

There are still many challenges with the Modern Workplace style of management, many of these can be addressed with PowerShell and some out of the square thinking. One thing I recommend when starting down this journey, is to spin up an [Azure Blob Store](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1) per customer, this will help with securing files, images, and any other content that you will need to pull down from a device potentially on any connection

[![Azure Storage]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AzureStorage.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/AzureStorage.png)

Azure Storage with Shared Access Keys opens up some valuable options for delivery
{:.figcaption}

## Windows 10

Windows 10 is the game changer for mobility in conjunction with everything we are talking about in this article. The whole ecosystem comes to life when you are running the latest Windows 10 release (currently 1803) and staying on top of the new capabilities. Unfortunately many of the important features and capability are not available in the current LTSC release which is based on Windows 10 1607, but the next release of this should  be out fairly soon which will open the door for enterprises consuming this LTSC release cycle to start embracing mobility entirely.

[![Windows10]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/Windows10.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/Windows10.png)

Windows 10. Get on it.
{:.figcaption}

The [Microsoft 365 Enterprise](https://www.microsoft.com/en-AU/Microsoft-365/enterprise) model is quite an appealing way for many organisations to move to this new way of consuming and working, it's worth a read on how it fits together here

## Defender Advanced Threat Protection

Windows Defender Advanced Threat Protection (ATP) is a unified platform for preventative protection, post-breach detection, automated investigation, and response.

[![DefenderATP]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/DefenderATP.png)]({{site.baseurl}}/assets/img/the-microsoft-modern-workplace-embracing-the-next/DefenderATP.png)

Defender ATP Threat Protection
{:.figcaption}

[Defender ATP](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) is one of the more interesting parts of the security stack for Microsoft. Its licencing is a little frustrating given that its either standalone, or as part of the Windows 10 Enterprise E5 licence SKU. But for those who want the complete Microsoft suite, its quite a powerful addition to your arsenal.

## Summary (For now)

This post is really just touching on the capability of the Modern Workspace at a 1000 foot level. The capability is changing almost weekly, and the roadmap looks bright. Resources such as the [Intune Roadmap](https://docs.microsoft.com/en-us/intune/whats-new), the [Azure AD Feature Page](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/whats-new) , [Office 365 Roadmap](https://products.office.com/en-us/business/office-365-roadmap), the [Azure Roadmap,](https://azure.microsoft.com/en-us/roadmap/) and finally the [EMS Cloud Blog](https://cloudblogs.microsoft.com/enterprisemobility/) are all great places to try and keep abreast of what’s going on.

There are awesome community resources out there to help with some of the technologies, my go to sites at the moment are typically (and I have missed some I am sure):

-  Aaron Parker - [https://stealthpuppy.com/](https://stealthpuppy.com/)
-  Peter van der Woude - [https://www.petervanderwoude.nl/](https://www.petervanderwoude.nl/)
-  Anoop C Nair - [https://www.anoopcnair.com/intune/](https://www.anoopcnair.com/intune/)
-  Oliver Kieselbach - [https://oliverkieselbach.com/](https://oliverkieselbach.com/)
-  Jos Lieben - [http://www.lieben.nu](http://www.lieben.nu)
-  Peter Dahl - [https://blog.peterdahl.net/category/enterprise-mobility/](https://blog.peterdahl.net/category/enterprise-mobility/)
-  Per Larsen - [https://osddeployment.dk/](https://osddeployment.dk/)
  
In closing, I will part with this: The Microsoft Suite of products here is quite possibly the best bang for buck out there. What impresses me the most about this stack, is that cool things keep popping up, and unless it is a monster feature like ATP, these features are simply ready for you to consume. I will also add that I have only touched on some of the capabilities here, anyone who works in this space will easily point out that there is way more in this suite, but that will be a post for another time.

Until next time, jump onboard and have a look at what is at your fingertips. Embrace the trip!
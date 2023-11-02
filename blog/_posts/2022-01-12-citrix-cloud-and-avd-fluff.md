---
layout: post
title: "Citrix Cloud and AVD - Absolute Fluff"
permalink: "/citrix-cloud-and-avd-fluff/"
categories: [Citrix, Windows, CVAD, Cloud, Azure, AVD]
description: Demystifying the cringe inducing fluff associated with Citrix Cloud and Microsoft AVD
image:
  path: /assets/img/citrix-cloud-and-avd-fluff/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Scene Setting

I am likely not winning many friends in certain circles with this article, but it is what it is.

This will be short and sweet, because the entire idea is to have a quick sanity check on what AVD is, what Citrix Cloud is, and demystify the marketing fluff that has statements spewing forth like:

> "Citrix Cloud enhanced AVD!"

Or:

> "Citrix Cloud and AVD, better together - hooray"

Or my personal favorite:

> "Citrix Cloud on AVD"

Or any of those deliciously rubbish myths that get thrown around and give me the shivers. Use this post to throw at those throwing out silly messages - it should help.

![Fluff](/assets/img/citrix-cloud-and-avd-fluff/memefun.jpg)

## What is Citrix Cloud?

[Citrix Cloud](https://docs.citrix.com/en-us/citrix-cloud/) is a collection of services delivered by Citrix, our focus here is on the [Citrix Virtual Apps and Desktops Service](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/overview.html), which provides the brokering, provisioning, scaling, policy and security engine that delivers virtual application and desktop environments. Citrix Manages a control plane, plugs into any Cloud or hypervisor (within reason) in any location, and allows you to centrally provision and manage workloads end to end.

## What is Azure Virtual Desktop (AVD)?

[Azure Virtual Desktop](https://docs.microsoft.com/en-us/azure/virtual-desktop/overview) is an Azure-native Virtual Desktop and application solution. It is built on Azure native services, offers a Microsoft managed Control Plane for proxying connections to Azure based virtual workloads. Microsoft define the solution as: `Azure Virtual Desktop is a desktop and app virtualization service that runs on the cloud.`

AVD supports Windows Server 2012 onwards, along within Windows 7 and Windows 10 single session along with Windows 10 or 11 (x) Enterprise Multi-Session.

AVD is **NOT** an Operating System.

## What's this Windows 10 or 11 Enterprise Multi-Session business?

This is where the confusion and misalignment tends to come in. Windows x multi-session, is the Windows Desktop OS with the brains of RDS layered in (there is more to it, but that's all that matters). You can only run it in Microsoft Azure (legally) and it provides a Windows desktop experience to multiple users on the same host.

Windows x Multi-Session is an entitlement. You can choose to use this entitlement however you see fit within the legal boundaries that Microsoft layout.

Windows 10 or 11 Multi-Session is **NOT** AVD.

## How does this AVD, Citrix Cloud and Windows x Multi-Session combine?

If you have an entitlement to run Windows 10 or 11 Enterprise Multi-Session you could choose to leverage AVD to broker your connections, or you could choose to leverage the brains of Citrix Cloud, to enhance provisioning, security, protocol maturity and Autoscale capability to enhance your VDI deployment in place of AVD. A direct replacement, not an enhancement or bolt on.

Citrix Cloud does not touch AVD in any way, shape of form. Never has, and to the best of my knowledge, never will (a brokering solution brokering another brokering solution?). Citrix Cloud can 100% take care of your entitlement to use Windows x Enterprise Multi-Session and plug a whole world of gaps and capability that has been built over 30 years of development and delivery.

## A quick summary of who does what

This is as close to a bakeoff as I am going, that is not the intention of this post. AVD is a great solution, typically you are going to need something to bridge the gaps and make it consumable at scale ([Nerdio](https://getnerdio.com/) or [WVD Admin](https://blog.itprocloud.de/Windows-Virtual-Desktop-Admin/) or [Project Hydra](https://github.com/MarcelMeurer/WVD-Hydra))

| | **Citrix Cloud** | **AVD** |
| **Control plane** | Managed by Citrix | Managed by Microsoft |
| **Access layer** | Citrix Gateway managed by Citrix | Azure POPs managed by Microsoft |
| **Workload platforms** | Hybrid: Azure plus on-premises, AWS, GCP | Azure only |
| **Windows 10/11 multi-session** | Yes, Azure-only | Yes, Azure-only |
| **Client** | Citrix Workspace app | Microsoft Remote Desktop app |
| **Client platforms** | Windows, Linux, macOS, iOS, Android, HTML5, Thin Clients | Windows, macOS, IGEL, HTML5, Android, iOS, Thin Clients |
| **Physical PC remote access** | Yes | No |
| **Linux virtual desktops** | Yes | No |
| **Optimisation** | Teams, Skype, Zoom, WebEx, Jabber, HTML5, etc. | Teams, Zoom |
| **License** | Citrix Cloud + Microsoft 365 | Microsoft 365 |
| **Image management** | Familiar tools and Citrix Machine Creation Services | Azure native tools requiring a cloud-native skillset |
| **Scaling** | Automated scaling and cost management with mature Citrix Autoscale | Limited scaling capability natively |
| **Protocols** | HDX | RDP |
| **Monitoring** | Citrix Director is familiar and provides rich metrics and reports | Azure Monitor and Log Analytics are challenging for staff not familiar with these tools |
| **Policy control** | Plenty of controls for secure access, peripherals etc. | Limited controls, mostly GPO and MEM |

## Summary

In closing, this is a very light touch article that is aimed to provide a bit of clarity and nothing more. Both solutions have their perks, I deal heavily in both and respect each for what they bring to the table. The aim here is to help wade through the confusion that has been floating around for a while now and keeps coming up in my conversations and engagements globally.
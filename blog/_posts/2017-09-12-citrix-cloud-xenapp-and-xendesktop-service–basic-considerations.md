---
layout: post
title: "Citrix Cloud - XenApp and XenDesktop Service - Basic Considerations"
permalink: "/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/"
description: "Some Citrix Cloud basics"
categories: [Citrix, Cloud]
redirect_from: 
    - /2017/09/12/110
    - /2017/09/12/110/
image:
  path: /assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

For anyone venturing into the world of the xenapp and XenDesktop Service in Citrix Cloud, you may find yourself a little confused at some of the terminologies and architecture outlines provided as part of the Cloud Service documentation.

The aim of this post is to try and outline in clear terms, what is possible, what is not possible, and some caveats around the xenapp and XenDesktop Service. I am all for Cloud Services, however I am more for understanding the Service and what is possible before buying into the dream. I am basing this post around existing on premise deployments looking to move to Citrix Cloud, not new deployments.

Let's start with what the xenapp and XenDesktop Service is, as defined by Citrix.

> The xenapp and XenDesktop Service is a service of Citrix Cloud. The xenapp and XenDesktop Service offers secure access to virtual Windows, Linux, and web apps and desktops. This service is based on xenapp and XenDesktop technology

Fairly straight forward right? Basically the service is a selection of components that you would traditionally deploy on premises to build a xenapp and XenDesktop Environment. These would typically entail at a minimum (for me):

-  SQL Server (2)
-  Citrix Controllers with Director (2)
-  StoreFront (2)
-  NetScaler (2)
-  Provisioning Server (2)
-  Workspace Environment Management (1)
-  Citrix Licence Server (1)
-  App Layering Appliance (1)
-  Profile Server (2)
-  RDS Licence Server (1)
-  VDA (n)

[![XA_OnPrem_Architecture]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/XA_OnPrem_Architecture.png)]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/XA_OnPrem_Architecture.png)

There is a lot of noise around how the Citrix Cloud service replaces your on premise components required to deploy a Citrix environment, however whilst this is true in some circumstances, let's take a look at what is required to provide traditional xenapp or XenDesktop Services in a like for like fashion using Citrix Cloud.

First, let me define a traditional deployment in a single line. A Citrix xenapp or XenDesktop deployment that delivers secure, fast and flexible access to desktops and applications on any device, anywhere, anytime. Straight out of Citrix Marketing right there. Now here is what is required to deliver that definition with Citrix Cloud

-  SQL Server (2)
-  Citrix Cloud Connectors (2)
-  StoreFront (2)
-  NetScaler (2)
-  Provisioning Server (2)
-  Workspace Environment Management (1)
-  Citrix Licence Server (1)
-  App Layering Appliance (1)
-  Profile Server (2)
-  RDS Licence Server (1)
-  VDA (n)

[![XA_Cloud_Architecture]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/XA_Cloud_Architecture.png)]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/XA_Cloud_Architecture.png)

"What?!" You say, why is the list above so big still, what have you taken away? Well let's see exactly what has and hasn't changed:

1.  SQL is still required for Workspace Environment Management, and Provisioning Services, so it stays
2.  Citrix Licence Server is still required for Workspace Environment Management and Provisioning Services, so it stays
3.  StoreFront, NetScaler, PVS, WEM, App Layering, Profile Servers, RDS Licencing and VDA's are all still critical components that I want (or need) in my deployments, so they all stay
4.  Citrix Controllers went, but the Citrix Cloud Connectors replaced them.
5.  Director went, but it was collocated with my controllers anyway

Let's delve into the details around why so little changed

## Architectural Overview (as per Citrix)

Consider the following items when setting up the xenapp and XenDesktop Service:

-  At least two Citrix Cloud Connectors are needed and can be placed in either the perimeter network (also known as a DMZ) or internal networks.

Let's be clear here, this actually means, that for every resource location you have, you are required to have 2 Cloud connectors. Azure, AWS, On premise = 6 Cloud Connectors.

-  One or more Linux or shared hosted desktop VDAs can be installed and configured for remote connections.
-  Connections to Storefront occur within the internal network Active Directory domain resource zone.

The keyword here is **INTERNAL**. Think `resource location`. By default, you have no remote access capability with the Service. You are required to deploy a NetScaler of some sort - either the Service, or an on premise NetScaler (2). Read below on some more caveats around this.

The below diagram greets you when you start to investigate the xenapp and XenDesktop Service, and things look great.

[![Citrix_Cloud_XAXDService_1]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_XAXDService_1.jpg)]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_XAXDService_1.jpg)

Note the term "**Internal**". It's important... [xenapp-and-xendesktop-service-getting-started](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/getting-started.html)

## Resource Locations

The definition of a resource location as per Citrix:

> Resource Locations contain the resources required to deliver services to your subscribers. You manage these resources from Citrix Cloud

You need the following components for your resource location:

-  An Active Directory domain controller.
-  Two physical or virtual Windows Server 2012 R2 machines that are joined to the domain, on which to install the Citrix Cloud Connector.
-  Two (Technically you only need one..) physical or virtual Windows Server 2012 R2 machines that are joined to the domain, for hosting application and desktop images.

## Citrix Cloud Connector

The definition of the Cloud Connector by Citrix, is as follows:

> The Citrix Cloud Connectors are proxies for communication between the Citrix Cloud broker, Storefront servers, and the VDAs.

[xenapp-and-xendesktop-service-getting-started](https://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/getting-started.html)

In my terms, the Citrix Cloud connector in its simplest form, takes the place of where your traditional xenapp or XenDesktop Controller would sit within the environment in each site. It plays the part of the Controller in the sense that your VDA's within your resource location register to it, in the same way that in a traditional deployment, your VDA's register to a controller within their site.

Furthermore, StoreFront (both on premise or cloud) talks to it in the same way that it would talk to a traditional Controller.

## StoreFront

The definition of StoreFront by Citrix, is as follows:

> StoreFront authenticates users to sites hosting resources and manages stores of applications and desktops that users access.

[xenapp-and-xendesktop-service-setting-up-storefront](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/setting-up-storefront.html)

This is where the waters can get a little bit murky. StoreFront can either be cloud based (Cloud hosted, patched, managed by Citrix) or it can still live on premises. Why would you want to deploy StoreFront on premises when there is the option for Citrix to host it? There are four main reasons:

1.  It offers greater security, including support for two-factor authentication and prevents users from entering their password into the cloud service. It also allows customers to customize their domain names and URLs. **This is recommended for any existing xenapp and XenDesktop customers that already have StoreFront deployed.**
2.  The Cloud Hosted StoreFront cannot aggregate resources from multiple farms. This is ok for a brand new deployment, but many, many StoreFront deployments consist of multi farm aggregation
3.  Customisation of StoreFront is not available at this point in time with Citrix Cloud Hosted StoreFront, Authentication limitations are also a potential concern
4.  Cloud-hosted StoreFront can only be used for **external or internal** access

Point four for me took a while to grasp. We can have cloud hosted storefront, and we can enable NetScaler services. Sounds good, until you read the caveat of point four which tells us that while we can of course, have a cloud hosted storeFront, we can't have cloud hosted storeFront if we want both Internal and External Access. Which is the majority of Citrix deployments I have heard of or been involved with. Realistically, you want StoreFront local to your resources anyway, and a lot of the larger deployments I have been hearing about with multiple resource locations are by default, ignoring StoreFront Cloud (which does align with Citrix Guidance once you read into it - Citrix aren't hiding anything here).

Conversely, The StoreFront Cloud service is actually publicly accessible via **<https://customername.xendesktop.net/Citrix/StoreWeb/>** however you can only launch applications if you have direct connectivity to your VDA's, so you still require a NetScaler (this is different than the xenapp or XenDesktop Essentials services, which includes the NetScaler Gateway Service by default)

[![Citrix_Cloud_Storefront]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_Storefront.jpg)]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_Storefront.jpg)

After we have read and grasped this, we now move onto NetScaler with the below in mind:

> Citrix Cloud comes with a cloud-hosted StoreFront that enables you to provide internal access to the applications and desktops you make available in your resource location. To provide external access to those resources, you need to add NetScaler VPX to your resource location and configure a NetScaler Gateway that your users can access.

and

> if you want to enable secure external access to the applications and desktops you offer to users, you will need to add a NetScaler VPX appliance to your resource location and set up a NetScaler Gateway.

and

> For proof-of-concept purposes, you can use the cloud-hosted StoreFront that comes with Citrix Cloud, which allows internal access only

There it is. The above comments just told us that we need to deploy NetScalers On Premise to provide secure remote access if we want **cloud hosted StoreFront** to provide **Internal access** (brain hurting yet?). The most common guidance and topologies from Citrix still recommend deploying StoreFront servers in your local resource location.

## NetScaler Gateway - Service or VPX

Let's again define NetScaler Gateway as per Citrix

> NetScaler Gateway provides users with secure VPN access to xenapp, XenDesktop, and XenMobile applications across a range of devices including laptops, desktops, thin clients, tablets, and smartphones.

Citrix offer two models of NetScaler as part of you xenapp Cloud subscription. The Traditional NetScaler VPX, and the NetScaler Cloud service at an additional cost. The xenapp and XenDesktop Service actually includes licences for six (6) NetScaler VPX Instances which you can deploy in your resource locations (ICA Proxy only)

The definition of the NetScaler Gateway Service as per Citrix

> NetScaler Gateway Service enables secure, remote access to xenapp and XenDesktop applications, without having to deploy NetScaler Gateway in the DMZ or reconfigure your firewall. The entire infrastructure overhead of using NetScaler Gateway moves to the cloud and hosted by Citrix.

Now as per, [xenapp-and-xendesktop-service-netscaler-gateway-as-a-service](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/netscaler-gateway-as-a-service.html)

> You enable NetScaler Gateway Service in the Citrix Cloud. After enabling the service, users can access their VDAs from outside their network, as shown in the following diagram.

[![Citrix_Cloud_NetScaler_1]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_NetScaler_1.jpg)]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_NetScaler_1.jpg)

What we need to focus on here, is the keyword of "Outside". What this doesn’t talk about clearly, is the fact that StoreFront in Cloud can only be Internal or External, thus you must have StoreFront on premises if you want internal access and external access for your Desktops and Apps.

Using the NetScaler Gateway Service avoids the need to deploy NetScaler Gateway within customer data centers. To use the NetScaler Gateway Service, it is a prerequisite to use the StoreFront service delivered from Citrix Cloud. The data-flow when using NetScaler Gateway Service is shown in the figure below

[![Citrix_Cloud_NetScaler_Flow]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_NetScaler_Flow.jpg)]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_NetScaler_Flow.jpg)

[xenapp-and-xendesktop-service-technical-security-overview](https://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/technical-security-overview.html)

If this is an OK situation for you, (and it may well be), then you should still be aware of the following limitations of the current NetScaler Gateway Service

-  NetScaler Gateway Service is enabled for use with HDX traffic as part of the xenapp and XenDesktop Service only. Other NetScaler Gateway functionality is not enabled.
-  The Citrix Cloud Connector located within your Citrix Cloud resource locations communicates with Citrix-run cloud services communicating through the internet. This communication channel does not support authentication at outbound proxies for access to the internet.
-  All network traffic is protected by SSL, but to provide the NetScaler Gateway functionality, HDX traffic is present in memory in an unencrypted form
-  To use the NetScaler Gateway Service, you must use StoreFront hosted within the Citrix Cloud.
-  Smart Access does not work for sessions connected through the NetScaler Gateway Service.

Do not be fooled into thinking you do not need a NetScaler on prem or that the licence provided via your xenapp and XenDesktop Service will cover you for many of the awesome features that NetScaler provides. You only get ICA proxy via both the service, and the VPX licences provided.

Be very careful around the limitations around the cloud service also, there is no multi factor Auth, no customisations etc. it is very basic at this point in time.

## Provisioning

As per Citrix

> There are two options for creating Provisioning Services managed VDAs from an on premise Provisioning Server deployment

-  XenDesktop Setup wizard in the Provisioning Services console
-  Machine Catalog Setup in Studio

The xenapp and XenDesktop Service in Citrix Cloud can provision and power-manage VDAs (Virtual Delivery Agents). For on-premises (resource locations) hypervisors, requests are proxied through the Citrix Cloud Connector. You can provision VDAs using the following methods:

-  Machine Creation Services (MCS)
-  Provisioning Services

I would like to highlight the above which I have heard some debate about when having technical discussions. PVS is still a completely valid provisioning service for on premise infrastructure when using the xenapp and XenDesktop Service. MCS is obviously the only true provisioning model for Public Cloud environments such as AWS and Azure, however contrary to the way its worded at times, PVS is still alive and kicking strongly and still makes complete sense.

But what about licencing? PVS licencing is validated against a Citrix Licence Server...still going to need one of those to use PVS.

Where do we put the database for PVS? We still need a SQL Server to house this database [xenapp-and-xendesktop-service-configure-provisioning](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/configure-provisioning.html)

If you are a new customer who doesn't have existing licences and has decided to deploy Citrix Cloud xenapp and XenDesktop Service and you want to use PVS, you will need to contact Citrix to supply you with a licence file for your licence server.

## Workspace Environment Management

The Norskale acquisition of VUEM and the re-branding and release of the product as Citrix Workspace Environment Management, a product which has changed the game entirely with its resource management and optimizing technology is now critical to any Citrix Deployment these days in my opinion.

So with that in mind, where do you place the database required by WEM? Obviously we still need a SQL Server.

How about licencing? WEM licencing is validated against a Citrix Licence Server, the same as PVS so you are still going to need one.

WEM is still completely alive and valid when we utilise Citrix Cloud, even more so when you think about density and compute costs. WEM still requires, in each resource location, at least one WEM broker.

If you are a new customer who doesn't have existing licences and has decided to deploy Citrix Cloud xenapp and XenDesktop Service and you want to use WEM, you will need to contact Citrix to supply you with a licence file for your licence server.

## Summary

This is a far more accurate reflection of a realistic Citrix Cloud deployment. This is dug up from the sizing and scalability PDF, however it doesn't identify a licence server requirement nor a SQL requirement (based on the optional use of PVS and WEM) [xenapp-xendesktop-service-sizing-scalability](http://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-xendesktop-service-sizing-scalability.pdf)

[![Citrix_Cloud_XAXDService_2]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_XAXDService_2.jpg)]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_XAXDService_2.jpg)

And for a touch more detail on what the Cloud Connector does:

[![Citrix_Cloud_XAXDService_3]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_XAXDService_3.jpg)]({{site.baseurl}}/assets/img/citrix-cloud-xenapp-and-xendesktop-service–basic-considerations/Citrix_Cloud_XAXDService_3.jpg)

As you can see, we still need NetScaler Gateway, Storefront, Workspace Environment Management (technically optional), Provisioning Services (technically optional), App Layering (technically optional), Cloud Connectors (replacing Controllers), Profile Servers as well as RDS Licencing. It's important to understand where and why they are still required.

What aren't mentioned but are still optionally required depending on your environment, is SQL (still need it for WEM and Provisioning Service) and Citrix Licence Server (still need it for WEM and Provisioning Services).

What we categorically don't need anymore is a traditional Controller, Director and Studio.

I hope the above helps when starting to look at Citrix Cloud. This service is a fantastic offering, and will continue to grow and develop over time. For me personally, the confusion around what is, and what is no longer required in deployments is one of the biggest challenges, both in wording as well as actual technical requirements.

As always, you should read up on the services as much as possible, and if you have the option to talk to Citrix guys, do so. This is a fast moving product and they are all over it at the moment.

A critical resource when comparing features is the below feature Matrix [xenapp-xendesktop/release-feature-matrix](https://www.citrix.com/products/xenapp-xendesktop/release-feature-matrix.html)

Good luck, and happy clouding

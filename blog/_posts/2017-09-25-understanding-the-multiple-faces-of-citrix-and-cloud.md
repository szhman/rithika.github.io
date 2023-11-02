---
layout: post
title: "The multiple faces of Citrix and Cloud"
permalink: "/understanding-the-multiple-faces-of-citrix-and-cloud/"
description: "Defining some of the Citrix Cloud Service Options"
categories: [Citrix, Cloud]
redirect_from: 
    - /2017/09/25/understanding-the-multiple-faces-of-citrix-and-cloud
    - /2017/09/25/understanding-the-multiple-faces-of-citrix-and-cloud/
image:
  path: /assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

As we move into a world of "Cloud" everything, the more vendors are producing offerings to cater for this ever increasing demand. Citrix are no different, they are well and truly on the cloud bus, and have been releasing offerings left right and center over the last period. Even the latest CEO took the time to post a blog about the Citrix Cloud Journey and how important it is to Citrix. This is a top down direction within Citrix, so worth taking note of. [Citrix Cloud Blog - David Henshall](https://www.citrix.com/blogs/2017/09/14/why-customer-are-demanding-citrix-cloud-hint-its-all-about-the-experience/).

There is coolade spilling everywhere, and buzzwords being thrown around like there is no tomorrow, so I spent some time recently picking apart the [XenApp and XenDesktop Service offering](https://jkindon.wordpress.com/2017/09/12/110/) provided within Citrix Cloud. This time I have decided to try and do the same for the other offerings and Cloud based deployment models that are available. Hopefully the below analysis can help to identify which, if any, Citrix based Cloud offering is right for you.

I am dealing with the four main offerings that I see called out. XenApp Essentials, XenDesktop Essentials, XenApp and XenDesktop Service and the typical "Citrix on Cloud" or more simply put: Hybrid model.

This is a great starting point when comparing features [XenApp and XenDesktop Deployment Features Comparison](https://www.citrix.com/content/dam/citrix/en_us/documents/reference-material/xa-xd-deployment-options-feat-comp-matrix.pdf)

## XenApp Essentials

### Overview

The XenApp Essentials offering from Citrix is a direct replacement for the original Microsoft RemoteApp product hosted in Azure. This solution provides a relatively simple and easy way to provide published applications, allowing you to leverage the scale and flexibility of Azure, without needing to manage the Citrix control plane.

XenApp Essentials works in conjunction with Citrix Cloud to provide Application publishing from an image that you build and manage within Azure. There is no such thing as published desktops with the XenApp Essentials offering, with its counterpart (XenDesktop Essentials) being the only mechanism to publish a desktop within the Essentials Suite.

You manage your application publishing via a web based console, not traditional Citrix Studio. Citrix also bundles in the NetScaler Service as part of the XenApp Essentials offering, which provides ICA proxy functionality behind the scenes. There is no "Internal/External" mentality with XenApp Essentials, all connections are treated as external and are established via the NetScaler Gateway Service and Cloud hosted Storefront.

An architectural overview of the XenApp Essentials Service inclusive of access back to On-Premises resources is found below.

[![EssentialsOverview]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/EssentialsOverview.png)]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/EssentialsOverview.png)

### Licencing

XenApp Essentials has two required licenced components:

1.  The XenApp Essentials offering, purchased through the Azure Portal and charged at a monthly cost, for a minimum of 25 users. Billing is handled by Microsoft
2.  Remote Desktop Services (RDS) CALS. This is a per user per month cost charged by Microsoft to allow you to use RDS Services to publish applications. This is no different than providing this service on a traditional RDS or Citrix deployment, however Essentials offers the flexibility to pay this monthly. You also have the option to bring your own RDS licences if they are under SA.

A breakdown of who gets what is shown below.

[![EssentialsLicencing]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/EssentialsLicencing.png)]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/EssentialsLicencing.png)

### Identity

The XenApp Essentials offering requires the identity source for your users to be either Active Directory, or Azure AD. You must have an Azure AD based account to start the setup process and you cannot use a non-Azure AD based Microsoft account. The use of Azure AD Domain Services is a little more tricky than setting up a Domain Controller, however it has the benefits of not requiring Domain Controller servers to be running. It’s a supported and documented model.

XenApp Essentials offers Profile Management via Citrix UPM, and you are required to configure a repository for your profiles in the same resource location that your workers reside. This is at your cost. Additional configurations such as folder redirection and environment control is configured via traditional Group Policies.

### Compute/Infrastructure Requirements

There are some basic infrastructure requirements to get the XenApp Essentials service up and running, these services will be charged to you by Microsoft. These are based on standard Azure pricing for your region. You can utilise existing credits for the Azure IaaS components, however these cannot be used for the Essentials Service itself

1.  Citrix Cloud Connectors (2) (These are deployed and managed automatically by the Essentials Service)
2.  Active Directory Domain Controllers (or Azure Domain Services)
3.  VPN Gateways (Optional) to provide connectivity between Azure on and On-Premises systems
4.  Remote Desktop Licence Server (1)
5.  XenApp Worker Servers (n)
6.  Profile Management Infrastructure (n)

A breakdown of who does what for you, and what you have to bring to the party is shown below:

[![EssentialsCompute]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/EssentialsCompute.png)]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/EssentialsCompute.png)

XenApp Essentials like any Citrix cloud offering, utilises MCS for its machine provisioning. You are required to provide an image with your applications installed and configured along with the Citrix VDA. Essentials via MCS will control machine provisioning and Identity based on the values you provide the wizard.

XenApp Essentials leverages tools such as Smart Scale from the Citrix Cloud suite, allowing for intelligent scale up and down to handle load requirements. It's important to keep in mind however, that you are dealing with front facing XenApp Worker servers, so scaling down in particular needs to be planned to ensure users are not adversely effected by a server powering down whilst they are active.

To access resources outside of your azure region such as internal application servers hosted on premises, you will require a VPN or ExpressRoute connection between Azure and your desired location.

A nifty calculator for your compute requirements can be found here [Cost Calculator - Azure](https://costcalculator.azurewebsites.net/costCalculator)

### Setup Process

There is a fantastic guide from Christiaan Brinkhoff on the end to end setup process of XenApp Essentials located here: [XenApp Essentials Setup](https://blog.infrashare.net/2017/07/05/how-to-configure-citrix-xenapp-essentials-in-microsoft-azure-including-azure-active-directory-authentication-to-citrix-cloud/).

The basic requirements to get up and running are:

1.  You need a Citrix Cloud Account
2.  You need an Azure Subscription
3.  You need an Azure AD Directory
4.  You need a Directory Service (Either Active Directory or Active Directory Domain Services) to house your users and required computer accounts

[XenApp Essentials Deployment Guide](http://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf)

### Benefits and Considerations

The XenApp Essentials offering has the following benefits:

-  It's extremely simple and easy to manage
-  It's flexible and scalable, as long as you design within the detailed capabilities

The offering also has the following limitations for consideration:

-  There is no Workspace Environment Management or App Layering provided
-  There is no NetScaler customisation, Storefront Customisation, Multi Factor Authentication capability at this stage
-  There are still traditional Server role requirements that need to be deployed for this offering to operate
-  Whilst this is driven via the Citrix Cloud XenApp and XenDesktop offering, it's very much cut down

### Where it fits

This is the direct replacement for Microsoft Remote App Collections. For customers currently requiring very simplistic publishing of simple applications, without the underlying maintenance of a citrix platform, essentials may well be worth looking at.

It's important to note that the Essentials offering cannot be used in conjunction with any other Citrix Cloud offering, meaning that If you choose to setup XenApp Essentials, the Citrix cloud account that you configure cannot be used to host the XenApp and XenDesktop Service in the future.

I had the opportunity to work with a customer who wanted to provide access to an application that they provided that has not yet been transitioned to a web based service. Essentials combined with Azure AD, Azure AD Domain Services, Azure SQL and some other Azure cloud services allowed them to offer a complete hosted multi-tenant solution for their product. On boarding and management was simple and they can scale very quickly. They ended up with the requirement of a single additional Virtual Machine to provide administration tools to manage Azure AD Domain Services, Group Policy and RDS Licence roles etc. Quite a neat solution

## XenDesktop Essentials

### Overview

NB: This section has been updated after having a good discussion with Christiaan Brinkhoff - Kudos to him for his time.

The XenDesktop Essentials offering from Citrix is the offering that is designed specifically for managing and delivering Windows 10 Enterprise virtual desktops on Azure.

You manage the solution via Citrix studio available within Citrix cloud itself, your Windows 10 workloads are housed in Azure.

An architectural overview of the XenDesktop Essentials offering is shown below:

[![XenDesktopEssentialsOverview]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/XenDesktopEssentialsOverview.png)]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/XenDesktopEssentialsOverview.png)

Information about the service can be found here [XenDesktop Essentials Info](https://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xendesktop-essentials.html)

### Licencing

XenDesktop Essentials has two required licenced components:

1.  The XenDesktop Essentials product, purchased through the Azure Portal and charged at a monthly cost, for a minimum of 25 users. Billing is handled by Microsoft
2.  Windows 10 Enterprise CALS. You must have an Enterprise Agreement with Microsoft to be able to leverage Windows 10 on Azure as part of this service

Note: There are some pending options around this space as per this [blog](https://www.citrix.com/blogs/2017/09/06/making-xendesktop-essentials-for-windows-10-on-azure-even-better/) from Citrix

### Identity

The XenDesktop Essentials offering has the same Identity requirements that the XenApp Essentials offering does, however given where its current positioned, its going to be far more common to simply utilise existing Directory Services extended across an ExpressRoute or VPN connection.

### Compute/Infrastructure Requirements

There are some basic infrastructure requirements to get the XenDesktop Essentials service up and running, these services will be charged to you by Microsoft. These are based on standard Azure pricing for your region. You can utilise existing credits for the Azure IaaS components, however these cannot be used for the Essentials Service itself

1.  Citrix Cloud Connectors (2) (these are deployed automatically by the Essentials Service)
2.  NetScaler VPX (2) these are deployed in ideally the same resource location as your Cloud Connectors. Optionally you can subscribe to the NetScaler Gateway Service
3.  Active Directory Domain Controllers (or Azure Domain Services)
4.  VPN Gateway or Express Route to provide connectivity between Azure on and On-Premises systems
5.  Windows 10 Virtual Machines (n)
6.  Profile Management Infrastructure (n)

XenDesktop Essentials utilises MCS in the same fashion as the XenApp Essentials offering for provisioning

### Setup Process

The basic requirements to get up and running are:

1.  You need a Microsoft Enterprise Agreement
2.  You need a Citrix Cloud Account
3.  You need an Azure Subscription
4.  You need an Azure AD Directory
5.  You need a Directory Service (Either Active Directory or Active Directory Domain Services) to house your users and required computer accounts
6.  You need to deploy a NetScaler VPX into Azure for remote access or subscribe to the NetScaler Service within Citrix Cloud

A basic guide for deploying NetScaler in Azure can be found here [VPX in Azure Deployment Guide](http://docs.citrix.com/content/dam/docs/en-us/workspace-cloud/downloads/NetScaler-VPX-in-AZURE-Deployment-Guide.pdf)

### Benefits and Considerations

The XenDesktop Essentials offering has the following benefits:

-  It's extremely simple and easy to manage
-  Its' flexible and scalable, as long as you design within the detailed capabilities

The offering also has the following limitations for consideration:

-  There is no Workspace Environment Management or App Layering provided
-  The solution by default is released without remote access. A NetScaler VPX must be deployed and configured to provide remote access, else the NetScaler Gateway Service can be consumed as per [here](https://www.citrix.com/global-partners/microsoft/windows-10-azure.html)
-  There is no Storefront customisation at this stage

### Where it fits

For customers wanting to consume not just traditional IaaS offerings in Azure, but Windows 10 desktops as well, this offering offers many of the benefits of Citrix Delivery services alongside Azure scale capability, this is the offering I hear least amount about.

As I understand it, this is positioned by Citrix for those Enterprise Customers that may already have a large presence in Azure, and have services such as ExpressRoute in and available.

## XenApp and XenDesktop Service

This is the underpinning technology that powers both XenApp and XenDesktop Essentials offerings, as well as a complete standalone full-fledged Cloud Solution provided by Citrix, completely independent of the Essentials offerings. I wrote a [post](https://jkindon.wordpress.com/2017/09/12/110/) recently about what this service is, and what it isn't.

This is the big boy of the Cloud Services model, however its important to understand what's in and what's not, and what you still need to cater for

[![XAXDService]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/XAXDService.png)]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/XAXDService.png)

The latest master-class for Apps and Desktops specifically discusses this model and is a good overview of the service. If you watch it, go and read my initial blog post on the topic to fill in the gaps [Apps and Desktops Master Class - Migrating to Citrix Cloud](https://www.youtube.com/watch?v=8Q906GVcyCY)

## Citrix On Cloud/Hybrid

### Overview

As per usual when dealing with cloud services, we get to throw out the term hybrid. This mode realistically is no different than having a normal on premises deployment, you just choose where to run certain components. This is more easily understood as "Citrix on Cloud".

For example, a typical model of this may be to deploy your Citrix Infrastructure components on premises, and run all, or some of your XenApp Workers in an alternate cloud - Azure, AWS, Rackspace, Private Clouds etc

Alternatively, you may choose to run one of everything in a cloud offering for HA . One node from each of your HA components could well live in Azure, or AWS - as long as you have network connectivity of a decent standard between sites that host your infrastructure and worker components, there is realistically no difference between this, and a multi-site Citrix Deployment

[![Hybrid]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/Hybrid.png)]({{site.baseurl}}/assets/img/understanding-the-multiple-faces-of-citrix-and-cloud/Hybrid.png)

### Licencing

There are no real Citrix based licence considerations when it comes to licencing in the Hybrid Model when you are just consuming compute based resources, however depending on which cloud you run certain Windows workloads and services, you may need to investigate what you can and cannot do with existing Microsoft Licences

### Identity

Identity requirements are the same as running a multi-Site Citrix Deployment, though ideally you want to have a Domain Controller living as close to your workers as possible. The same can be said about many of the components in the Citrix stack

### Setup Process

The setup process for this model has little difference to that of a traditional Citrix Deployment. Workloads are provisioned via different mechanisms depending on your cloud of choice, however conceptually its all the same

### Benefits and Considerations

The Hybrid deployment model has the following benefits:

-  It's everything you know about Citrix, just deployed in different locations. No real difference other than having to cater for the intricacies around your cloud of choice
-  Availability Sets, Scale tools, backup services etc.
-  It's extremely flexible with scale
-  firing up compute resources is relatively fast
-  scaling them is almost limitless
-  There is no limit to how many providers you consume resources from.
-  When Architected properly, the Cloud providers are conceptually nothing but another Data Center at your disposal

The model also has the following factors for consideration:

-  You must have adequate network connectivity between your resource locations. This is the same as pretty much any IaaS offering that needs to integrate with On-Premises systems
-  There is limited ability to share images easily between on premises and cloud systems - this means that you need to prepare either multiple images to cater for your workload placement, or you need to export and prepare your master images from on premises to be used with Cloud systems - not hard, but time consuming
-  PVS does not exist for IaaS offerings, we are limited to MCS - not a show stopper even if you are using PVS on premises, but a consideration
-  Compute costs need to be controlled. Important to think about strategic scale up and down techniques
-  App Layering can help here with cross platform providers
-  WEM may need multiple configuration sets to provide against different machine specs (particularly around performance management)
-  You want to keep resources that impact user experience as close to the workers hosting their sessions as possible - this includes services such as Storefront, Profile Management Stores, Applications etc
-  Technologies such as Application Virtualisation, Containerisation, App Layering etc become more and more prevalent the more you start thinking about the cross cloud requirements. The more you can move towards an OS + Layers model, the more smooth your cloud adventures will be from a XenApp Worker perspective. Automation of these layers is critical
-  Some cloud providers will give you no ability to use provisioning tools, PVS or MCS. This is an extremely important part of entire Citrix solution and without it, things are not ideal. It's important to factor this in, and negotiate a level of access that will allow you to use provisioning where possible. Based on my experience, I wouldn't select a cloud offering that doesn't let me use at least MCS

Dan Feller has released a really good blog post on sizing and costing of XenApp VM's in Azure, its a very interesting read found here [Sizing Azure VMs for XenApp](https://virtualfeller.com/2017/09/26/sizing-xenapp-azure-vms/)

### Where it Fits

Many organisations I have spoken to tend to start talking about Cloud offerings as a DR option for the their on-premises Citrix Deployments. Having compute on standby rather than running full time is often far more appealing from a dollar figure perspective on paper, however it's important to really consider what you need to do to maintain your DR process with a cloud platform in the mix. There may be a significant amount of configuration that is required to ensure a smooth DR, and often this configuration must be updated every time you make a production change. When you start thinking about how many moving parts there are in a Citrix platform, the idea of moving things to the Cloud can be rather daunting.

Other customers are already fully in the Public Cloud space. We hear more and more about companies running minimal infrastructure on premises, and a massive amount of workload in Azure or AWS, inclusive of all Citrix components - NetScaler, Controllers, Storefront, SQL, WEM - all of it. This is more of an all in model, and typically reflects the on-premises deployment architectures, with the additional Cloud considerations taken into account

## In Closing

So many offerings, so many options, so many considerations. This whole cloud business is pretty confusing, and with the release of so many different options and services by Citrix in such a short time, most people I speak to are a little in the dark about what is what. As always, I hope this provides a little clarity on your options and where they fit for you, or your clients.

---
layout: post
title: "Citrix Cloud: Resource Location Decision Flow Charts Revamped"
permalink: "/citrix-cloud-resource-location-decision-flow-charts-revamped/"
description: "Updated decision making process for Citrix Cloud resource location requirements"
categories: [Citrix, Cloud, WEM, XenApp]
redirect_from: 
    - /2018/11/20/citrix-cloud-resource-location-decision-flow-charts-revamped
    - /2018/11/20/citrix-cloud-resource-location-decision-flow-charts-revamped/
image:
  path: /assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

A while ago I put together a couple of posts around the [basics of the XenApp and XenDesktop Service](https://jkindon.com/2017/09/12/110/) as it was known as back then, as well as a [high level decision flow chart](https://jkindon.com/2017/11/06/citrix-cloud-resource-location-requirements-decision-flow-chart/) to identify local resource requirements when dealing with Citrix Cloud.

Considering those pieces were written over a year ago, I thought it was well past due to update that map with the changes that have occurred over the last 12 months and provide a clearer view on how things currently sit, as well as what you need to be aware of when deploying Citrix Cloud based Apps and Desktops solutions.

I have rewritten the decision flows as I see them, and structured them with the following breakdowns:

1.  Per Resource location requirements
2.  Provisioning Technology requirements
3.  Application Deployment and Desktop Optimisation requirements
4.  Internal Access requirements
5.  External Access requirements
6.  Analytics and Control Access requirements

Below is a breakdown and run through of each section.

## Per Resource location requirements

At a bare minimum, each resource location that you configure in Citrix Cloud has the following requirements:

-  2 x Citrix Cloud Connectors. These are Windows Servers which broker communications to the Citrix Cloud Services. These should be dedicated Servers as Citrix will release updates as they see fit
-  Citrix Virtual Delivery Agents. These are the Servers or Desktops provide users with their resources
-  An Active Directory presence

Those with a keen eye will note that I do not specify services such as file servers, profile management locations etc. This is by design, I am not including them as they are not necessarily mandatory requirements, and different flavours will have different requirements. These are different decision factors against different requirements in my eyes.

[![Phase 1 - Procurement and Resource Location Essentials]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase1-Procurement.png)]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase1-Procurement.png)

Phase 1 - Procurement and Resource Location Essentials
{:.figcaption}

## Provisioning Technology requirements

This is part 2 of the decision flow, and there are some immediate impacts against each resource location in relation to what needs to be deployed and managed within that location.

[![Phase 2 - Provisioning]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase2-ProvisioningTechnology.png)]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase2-ProvisioningTechnology.png)

Phase 2 - Provisioning
{:.figcaption}

**Decision 1.** Is Citrix Provisioning Services going to be utilised to control the provisioning of images and workers? If the answer is yes, then it will be required to deploy the following within each resource location utilising this service. (Please note that to consume the Cloud provided Provisioning Server Licences, a minimum of Provisioning Server 7.18 is required)

-  2 x Citrix Provisioning Servers (HA)
-  1 x Citrix Licence Server
-  1 x SQL Database Server

If Citrix Provisioning Services is not required, move on.

**Decision 2.** Is Machine Creation Services to be utilised to control the provisioning of images and workers? If the answer is yes, then access to the appropriate Hypervisor or cloud based APIs (vSphere, Hyper-V, Citrix Hypervisor, Nutanix, Azure or AWS) is required. There is no other resource location based requirements from a Citrix Standpoint in relation to provisioning.

If utilising neither PVS or MCS, and simply performing manual provisioning steps, then there are no additional considerations required for this section and its time to look at the next phase.

## Application Deployment and Desktop Optimisation requirements

Part 3 of the decision flow deals direction with Desktop Optimization and Application delivery solutions, specifically, [Citrix Workspace Environment Management (WEM)](https://docs.citrix.com/en-us/workspace-environment-management/service.html), and [Citrix App Layering](https://docs.citrix.com/en-us/citrix-app-layering/4/manage/app-layering-services.html), both of which are now provided as a Service via Citrix Cloud.

[![Phase 3 - App deployment and Optimisation]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase3-AppDeploymentandOpt.png)]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase3-AppDeploymentandOpt.png)

Phase 3 - App deployment and Optimisation
{:.figcaption}

**Decision 1.** Is Citrix WEM to be utilised as part of the deployment? If yes, then simply enable the service, update the Cloud Connectors within each resource location (though they are most probably already up to date) and the resource location is prepared. Everything you know about WEM is the same, we just no longer need to worry about a Licence Server, SQL Database or WEM Brokers on Premise. The Cloud connector takes the role of the WEM broker and proxies these connections to the Cloud.

If WEM is not a requirement, then there is no impact to the resource location.

**Decision 2.** Is Citrix App Layering to be utilised as part of the deployment? If yes, then management of the App Layering environment is handled via the [Citrix App Layering Service in the Cloud](https://docs.citrix.com/en-us/citrix-app-layering/4/manage/app-layering-services.html), however you will need to deploy the following in your resource location:

-  An App Layering appliance locally to handle the App Layering tasks

If neither WEM or App Layering are required, then there are no impacts on your resource locations and you can move to the next phase.

## Internal Access requirements

Part 4 of the decision flow moves to address how users to consume their applications and desktops, when on the internal network.

[![Phase 4 - Internal Access]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase4-InternalAccess.png)]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase4-InternalAccess.png)

Phase 4 - Internal Access
{:.figcaption}

**Decision 1.** Is integration with Azure Active Directory, FAS and SSO functionality required? If yes to this requirement, then immediately the following requirements appear for each resource location:

-  2 x Citrix Federated Authentication Servers (HA)
-  2 x Citrix Storefront Servers (HA)
-  A load balancer of some sort to load balance Storefront (ideally a Citrix ADC)

A Microsoft Active Directory Certificate Services deployment underpins FAS and SSO, but I make the assumption that if you require this specific functionality, you already have this in place.

Currently, as at time of writing, Citrix Workspace does not support Federated Authentication Services or Beacons, leading to no SSO capabilities when accessing desktops configured against Azure Active Directory authentication sources within Citrix Cloud.

**Decisions 2, 3 & 4.** Is Local Host Cache capability required? Does the environment need to cater for both internal and external access (see use case 3 [here](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/storefront.html))? Is advanced customisation against your access portal required for corporate governance?

If the answer is yes to any of the above, then the following is required at each resource location:

-  2 x Citrix Storefront Servers (HA)
-  A load balancer of some sort to load balance Storefront (ideally a Citrix ADC)

If none of the outlined requirements exist, then users can consume the Cloud based Workspace solution in place of traditional Citrix Storefront. There is quite a unique scenario.

Next, we move to address external access requirements for the solution.

## External Access requirements

Part 5 of the decision flow moves to deal with external access requirements for the solution. These will be directly impacted by the internal access requirements noted in the previous phase.

[![Phase 5 - External Access]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase5-ExternalAccess.png)]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase5-ExternalAccess.png)

Phase 5 - External Access
{:.figcaption}

**Decision 1.** Is remote access to the environment required? If no, then it may be sufficient to simply consume the Cloud based Workspace solution in place of traditional Citrix Storefront (based on what was identified in the internal access decision flow), however if remote access is required, we need to address a few more considerations.

**Decision 2.** Is Multi Factor Authentication (RADIUS) required. If yes, then the following requirements appear for your resource locations:

-  2 x Citrix Application Delivery Controllers (NetScaler)

Note that we have already address the requirement of both internal and external access requirements in the internal access decision phase, and as such may have local Storefront available to utilise already.
{:.special_note}

If MFA is not required (Specifically RADIUS based MFA), then consumption of the [Citrix Gateway Service](https://docs.citrix.com/en-us/netscaler-gateway/service.html) in place of a traditional NetScaler ADC may be enough. It is important to be aware of the limitations of the Service and implications of those limitations against your deployment. As at time of writing, there is no support for RADIUS authentication with the Citrix Gateway Service.

## Analytics and Control Access requirements

The final part of your decision flow is a simple one.

[![Phase 6 - Analytics and Control Management]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase6-Analytics.png)]({{site.baseurl}}/assets/img/citrix-cloud-resource-location-decision-flow-charts-revamped/Phase6-Analytics.png)

Phase 6 - Analytics and Control Management
{:.figcaption}

Is Citrix Application Delivery Management (NetScaler MAS) required? If yes, then there are two consumption options:

-  Consume the Cloud Service
-  Deploy Citrix Application Delivery Management in your resource location. This is an appliance based deployment

At this point you should have enough information to understand the impacts and requirements of each decision you make in relation to Cloud services.

## Summary

Hopefully again, this high level decision process will help you clearly identify where you stand with Citrix Cloud Offerings and the requirements that you will need to meet from a resource location perspective.

The landscape is changing quickly, so I will attempt to keep this content up to date as solutions mature and limitations disappear.

Some of the images in this post might not scale well so I have attached a PDF copy [here](https://1drv.ms/b/s!Aias4D6K7Iqe9n3cqbDPFHfmdLQz) for those that need it

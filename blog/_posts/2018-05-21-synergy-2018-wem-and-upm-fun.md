---
layout: post
title: "Synergy 2018: WEM and UPM Fun"
permalink: "/synergy-2018-wem-and-upm-fun/"
description: "Some announcements from Citrix 2018"
categories: [Citrix, Cloud, FSLogix, Office365, WEM, Windows]
redirect_from: 
    - /2018/05/21/synergy-2018-wem-and-upm-fun
    - /2018/05/21/synergy-2018-wem-and-upm-fun/
image:
  path: /assets/img/synergy-2018-wem-and-upm-fun/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

As a first timer to Citrix Synergy this year, I was blown away at the pure scale of the event that Citrix put on. I expected the event to be awesome, but had no idea how amazing not just the conference is, but how priceless the chance to meet with the community, and the inner team around the products we work day to day in.

Obviously for myself, WEM is a huge focus area, and there were some significant announcements made in this space at the [SYN231](https://www.youtube.com/watch?v=kX72r3wBhBI) Session led by Pierre and Wayne. These announcements have some significant impact on not just the future of WEM, but the placement of supporting technologies within your infrastructure stack moving forward. I am going to dive in to the bits that I found exciting

## The WEM and UPM Merge

A quickly brushed over point in the presentation was the news that the UPM and WEM teams are now one, combined as part of the Unified Endpoint Management brand, which is consisting of WEM, UPM and XenMobile.

This is really a significant and logical move by Citrix and means that the combined brain power of the WEM and UPM development teams are set to continue building and releasing some pretty cool features in the not too distant future. Both of these solutions are critical to a good user experience and are already tightly coupled from a sense that WEM can drive UPM completely as it currently stands. When dealing with environment management, it makes sense that the user profile is added in to the "environment management" component, UPM is hereby known as a feature of WEM…I kind of like it.

## Cloud Service

In the current landscape, regardless of if you are consuming Citrix in a traditional manner (IaaS, On-Prem In-House etc) or if you are consuming Citrix Cloud, the deployment model for WEM is the same:

-  The requirement for a database
-  A server or two running the WEM Broker role
-  A load balancer for the bigger deployments
-  The WEM agent itself

This changes with the announcement of the WEM Cloud Service, where we are now set to be able to consume WEM natively as a cloud offering, with no dependency on any standalone infrastructure.

The offering itself is set to be driven via the existing Azure based backend that currently drives the current Citrix Cloud offerings, and a simple upgrade or clean deployment of an updated Citrix Cloud Connector. An updated ADMX template will be shipped when the release goes GA, which will allow you to specify the Cloud goodies for your brokering needs.

Importantly there is confirmed support for a full migration to the cloud offering via the standard WEM import and export options for configuration settings, as well as a full Database Import service to be provided for those with complex environments. The same console will be made available via Citrix Cloud to manage your environment, so everything you know to date stays the same.

The new WEM cloud offering is still "TBD" as far as commercials go (bean counter territory I would suggest), but I would be pretty disappointed if it's not included in the existing XenApp and XenDesktop Cloud entitlements. There are plans for bundled offerings, however we wait with baited breath to see how this pans out commercially.

Finally, as with most Citrix Cloud features, the code release flow will be with a Cloud First strategy (read: Guinea Pigs), with an update to on premises deployments shortly after the cloud release. The same code branches will be utilised to ensure consistency.

## Agent Auto Update

If you listened closely to the presentation, Pierre dropped that there is an Auto Update component coming for the WEM agent, I would love to see this morphed into a situation where WEM could push the agent from the console itself in the future.

## Physical Endpoint Support

There has been a noticeable deafening silence on the support of WEM on non VDA machines in the Citrix space, and a growing interest in managing the physical desktop fleet in the same manner that we do the Virtual. Particularly for those with large deployments of remote PC who really have no true differentiator other than tin. "Technically" this works perfectly fine, however there has been no clarity on what is OK, and what is not OK from a licencing standpoint.

It has now been confirmed that via platinum licencing, you are entitled to run the WEM endpoint on your existing physical endpoints, with the expectation that you don't exceed your existing Citrix Licencing capability. For more detail than that, watch the WEM space for announcements, else contact your account rep and harass them for details.

It has also been planned now with the release of the Cloud Service, to implement management and deployment of the WEM agent via the XenMobile (or whatever its now called) Serviceas part of the unified endpoint solution. This is nice for those that are looking to move to workspace offerings and have an interest in what WEM can offer to your fleet. The model will be one that offers you two ways to manage your windows fleet - modern management via the basic MDM functionality built into Windows 10, or full WEM management for those that want more. This is a cross team initiative with the WEM and XenMobile teams.

## Container Capability (Outlook OST and Windows Search)

Anyone who has read any of my soap box rants to date will know that I am a huge fan of the FSLogix Suite of products, and it's something that I try and deploy in all of my engagements when dealing with Office 365 and Citrix Application and Desktop Delivery. You can read up on why [here](https://jkindon.com/2018/02/20/getting-to-know-fslogix-containers/).

Probably the biggest announcement for me in the UPM space is the ability to now roam the Outlook OST and the Outlook Search Index for the users natively using UPM driven containers (VHDX files), very similar to the way in which FSLogix addresses the existing challenges with their product (however limited currently to just the OST and Search Index). This feature was announced in the WEM session; however, this is a Citrix Profile Management Solution, and does not require WEM to be in play for the feature to be consumed.

The containers will be created and managed by UPM as part of the user’s profile, and appears logically to be a combination of the new "Large file handling" feature of UPM and brand-new functionality built into the solution.

This isn't the first attempt by Citrix to address the "OST Demon", the App Layering team introduced an Office 365 Layer to assist here, however it missed the all-important search index roaming, and required you to deploy the app layering infrastructure simply to address the containerisation of the OST. hmmmm.

This announcement opens the door for many organisations that cannot afford the additional (though minimal and well justified) cost of FSLogix Office 365 Containers, and now provides an option to address the horrid performance challenges of Exchange Online in Published environments, all natively within the Citrix Ecosystem.

Does this put FSLogix on the ropes? Absolutely not, their product is mature, simple, proven and addresses a range of scenarios and technologies that are critical to a successful Office 365 deployment. Outlook and Search is just one component that they address with their Suite, however they still have plenty more to give in the 365 space (OneDrive, Skype, Licencing, OneNote), as well as plenty of other tricks up their sleeves with Profile Containers and Application Masking etc.

When I look at the landscape now and the options available to customers, the world is a substantially different place than it was 18 months ago. For the younger generation who are reading this, I have included a range of emoticon images to describe my current feelings towards it all.

![Happy Pic](https://jkindon.files.wordpress.com/2018/05/happy-pic.png)

For those that want an actual read up, then I suggest the mighty David Wilkinson’s blog post [here](https://wilkyit.com/2018/01/19/office-365-in-non-persistent-environment-product-comparison-matrix/).

What does the future hold here for Citrix? Well... you can guess as well as me, it's not a long jump from what they have achieved already to full profile containers, or even more controlled selective container use. I am excited to see where this goes

## Future of Transformer and Desktop Lock

The final interesting thing I picked up on was that Citrix Desktop Lock will be replaced by the Transformer product moving forward. This makes sense given then capability of the WEM Transformer Solution, compared to the simplicity of Desktop Lock. So, for customers out there utilising desktop lock in their current deployments, it's time to take note of the Transformer option and what it can bring to the table

## Final Thoughts

I was really fortunate to spend some time with awesome guys at the conference, and my feel is that the future is bright for Citrix and WEM. There was an openness to the sessions that at times defied the marketing fluff typically shipped around at these conferences, and the "say it how it is" attitude that was presented not only from the architecture and product manager standpoints, but from those that have been dealing with this solution far before the acquisition, made it an absolutely priceless experience.

I'm Looking forward to seeing what the next 12 months look like in this space, and already counting down the days till a return to the States for another Synergy in 2019, and another trip to the bar with the lads

---
layout: post
title: "Synergy 2019 Recap – The fun bits"
permalink: "/synergy-2019-recap-the-fun-bits/"
description: "Fun and relevant stuff I got out of Citrix Synergy 2019"
categories: [Apps and Desktops, Azure, Citrix, Cloud, Synergy, Windows]
redirect_from: 
    - /2019/05/27/synergy-2019-recap-the-fun-bits
    - /2019/05/27/synergy-2019-recap-the-fun-bits/
image:
  path: /assets/img/synergy-2019-recap-the-fun-bits/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Another year, another chaos jump across the ocean to the USA for Citrix Synergy. I was lucky enough to attend as a CTP this year, a completely different view on the conference from last year's visit.

Citrix made some big announcements, which you will no doubt have seen in a range of blogs, twitter posts, recap videos or any other means of content replay out there, so the same as last year, I am going to give a quick recap and update on things that I think are cool.

MicroApps/Intelligent Workspaces was Citrix's flag ship go to this year, a continuation of their messaging from last year around workspace and the "new way of working". This will obviously delight customers with large user bases, lots of SaaS apps and an Internal team that can work with the likes of Business Analysts to build out some simplified workflows for tasks that typically require multiple systems and touch points. Citrix are obviously hedging their bets on this being a game changer.

 Being a technical guy focused for many years on the Apps and Desktops side of things, my interests were far more piqued at any announcements around the maturity or addition of features to the Citrix Cloud space, in particular the Virtual Apps and Desktops Service and there are a few huge announcements which will completely change the conversations around the CVAD Service.

## **Cloud Enabled FAS for Workspace** **(Woohoo) and Identity enhancements**

In short, this was my favourite announcement this year, and the single most awaited feature causing the most frustration when talking to customers around StoreFront vs Workspace, Cloud vs On-Prem etc.

This conversation has just been thrown on its head. A 500 foot view of the feature is simply this:

-  FAS Servers can be deployed in the same architecture as they currently are into your resource locations
-  The existing FAS code base has been extended to allow a secure connection directly to the Workspace
-  Workspace is now fully enabled for FAS, no more direct requirement on StoreFront

So for those customers utilising ADFS, or Azure Active Directory (most of our cloud customers), we now have feature parity from a FAS perspective for Workspace - this is awesome!

[![Cloud Enabled FAS]({{site.baseurl}}/assets/img/synergy-2019-recap-the-fun-bits/CloudFAS.png)]({{site.baseurl}}/assets/img/synergy-2019-recap-the-fun-bits/CloudFAS.png)

The session on identity with Oscar Day and Rick Feijoo at [SYN108 - What's new in Citrix Cloud identity](https://www.youtube.com/watch?v=rFanC0_yp5c) was an absolute beauty, the basic outline of how identity fits together by comparing it to the USA Passport helped fill a few gaps in my own head. There has been an enormous focus on identity and extending out to the big players and I highly recommend watching the session [here](https://www.youtube.com/watch?v=rFanC0_yp5c)

The secondary identity change of interest was the ability to leverage an existing (or new) Citrix Gateway with AAA authentication services directly for Workspace. This allows an OAUTH connection between the Workspace and existing Citrix ADC Gateway, opening a world of fine grained identity controls.

[![AAA]({{site.baseurl}}/assets/img/synergy-2019-recap-the-fun-bits/AAA.png)]({{site.baseurl}}/assets/img/synergy-2019-recap-the-fun-bits/AAA.png)

## **Analytics**

The Citrix Analytics platform is something that smells of massive potential. Amidst everything new Citrix are pushing along with the obvious attempt to reinvent themselves, the Analytics Service will most probably be the common touch point for all their offerings.

Apps and Desktops aren't going away any time soon and the extension of some data into Analytics to help with UX measurements and basic troubleshooting was nice to see. What was even more pleasing is that Citrix have accepted that these services are also valuable to those customers who simply choose not to move their environments to the CVAD service. [SYN109 - Monitoring in an analytics and data-driven world](https://www.youtube.com/watch?v=o_5Yi4N8Po4) is worth having a run through. Of particular interest is the User-Centric experience scores.

[![Analytics]({{site.baseurl}}/assets/img/synergy-2019-recap-the-fun-bits/Analytics.png)]({{site.baseurl}}/assets/img/synergy-2019-recap-the-fun-bits/Analytics.png)

## **Windows Virtual Desktop**

The next big potential disruptor to the industry if done right, WVD obviously raises questions for almost every Microsoft Customer on the planet. What is it, where does it fit, is it a Citrix Killer, when, where, how, why…. Citrix sold a message of embracing this new platform from Microsoft and as always will continue to be a value add on top of the basics, be it experience, security, workspace integration and analytics etc, it will be interesting to see what this looks like in the next 12 months

[SYN139 - Citrix and Microsoft: a value-add across your workspace](//d.docs.live.net/9e8aec8a3ee0ac26/Documents/Personal%20-%20Kindon/Blog.one) around the 52 minute mark gets into the good stuff.

[![WVD Value Add]({{site.baseurl}}/assets/img/synergy-2019-recap-the-fun-bits/WVD.png)]({{site.baseurl}}/assets/img/synergy-2019-recap-the-fun-bits/WVD.png)

## **Citrix Managed Desktop Service**

[SYN111 - Desktops-as-a-Service with Citrix](https://www.youtube.com/watch?v=GE7aS5ltwdg) was an interesting session to help wrap your head around the future of the Citrix Managed Desktop Service (Daas). Interesting model where Citrix owns end to end delivery of the Desktop inclusive of Azure, customer simply brings Active Directory or Azure Active Directory and machine images.

[![DaaS]({{site.baseurl}}/assets/img/synergy-2019-recap-the-fun-bits/DaaS.png)]({{site.baseurl}}/assets/img/synergy-2019-recap-the-fun-bits/DaaS.png)

## **Google Cloud Platform**

Last year's keynote had an enormous focus on the future of Citrix and Google, which then appeared to gently slide back down into quietness very quickly. This year however there was some decent announcements of interest to those in my space:

-  Machine Creation Services for GCP
-  Citrix ADC HA support for GCP
-  Google as an IDP for Workspace

Features bringing GCP up to a first class Citizen in the Citrix ecosystem.

The [opening keynote](https://www.youtube.com/watch?v=an53Vd_lMTI) had arguable the best outline (that I saw) of feature announcements for GCP

## **Summary**

Synergy was again a great event which I took a lot away from, however its biggest value add was providing a single space for so many amazing minds and personalities to land together, the value of the conversations that were had outside of a keynote speech or official sessions is absolutely priceless, access to product managers and individuals involved in development is amazing.

Citrix obviously have a huge shift of direction, and the technologies and capabilities they are releasing are interesting. What is far more interesting to me is what does the next 2 or 3 years look like from adoption, implementation, feature enhancement and company direction perspectives. Beauty of Synergy is you get a very very clear view very quickly on what that is - so bring on Orlando 2020

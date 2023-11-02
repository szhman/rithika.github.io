---
layout: post
title: "What in the RAS?"
permalink: "/what-in-the-ras/"
categories: [Parallels, RAS, EUC]
description: A very late discovery of Parallels RAS
image:
  path: /assets/img/what-in-the-ras/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro to RAS

Parallels RAS has been a technology that has always sat on the periphery for me, its name floating around, and its messaging simple "*When you have had enough of Citrix, we are here*". At least that's how I always thought of it.

My assumption (sorry in advance) was that this was a "*make RDS cool again*" sort of technology, a "*good enough is good enough*" sort of caper. I missed the mark pretty hard.

I can quite openly say that one of the silliest things I have done in my EUC career to date is to not pay attention to these guys. I am somewhat embarrassed at how late I am to the game here. But here we are, and here is why I really like what these guys and gals have to offer, and how far more advanced they are than I gave credit for.

## Licencing, Entitlements and Support

First of all, here is the simplicity of their [product licencing](https://www.parallels.com/au/products/ras/buy/). There is one Sku and absolutely no complexity.

-  Concurrent Licencing only.
-  One size fits all - you get everything - no tiering, no hidden anything. All yours.
-  A minimum of 15 users buy-in (yep, 15 concurrent users).

[![RAS Licencing]({{site.baseurl}}/assets/img/what-in-the-ras/Licencing.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/what-in-the-ras/Licencing.png)

Complex? Didn't think so. Get your licence, stick it in the RAS console and off you go. I feel like I need to write more, but that's about all there is. Now onto the nerd stuff.

## Deployment and Components

Thought that licencing was simple? The actual architecture and setup of the tooling are almost on the same wavelength. You can get complex with this if you need to, but the barebone basics are this:

-  Windows-based management tool. Download the MSI, install, plug in a licence key and get going (to be fair, you can get going on a trial also).
-  You can do an all-in-one box with most roles/components, or if you need to separate for scale or security, then you can do that also. Same installer, with different components.
-  Linux-based HA/LB appliance. Download a turnkey Linux appliance, deploy it onto your hypervisor of choice, give it an IP address and connect it to the RAS console. The rest is done from there.
-  RAS uses Providers to talk to hypervisors - in the Citrix world, we call them hosting connections - same thing, different smell. Choose your poison, they are all there, Nutanix AHV (Whoop), VMware, HyperV, Azure, AWS, and even Azure Virtual Desktop (more on this later).
-  Clients connect to the RAS environment via a dedicated win32 client, or via a very nice HTML 5 browser client. Both are customisable and controllable.
-  They have monitoring and reporting included. Monitoring is a small additional deployment using Influx DB, Telegraf and Grafana to capture real-time metrics from the agents. Reporting requires a bit of SQL and SSRS love.
There is as much complexity as you want or as much simplicity. For small shops, you could likely get away with a couple of servers for redundancy and be done with it.

## RDSH, Apps, VDI and Remote PC

"*Make RDS cool again*". What a failure on my part. RAS doesn't just poke around at RDS, it offers:

-  RDSH with all sorts of additional capabilities including autoscale and non-persistent workloads (like MCS). Both published desktops and published applications (did I mention this works very nicely with AHV)?
-  Application publishing extends to AppV packages, Web Apps, documents and folders (published content, anyone?).
-  VDI for Windows Client based VDI workloads. These can be both persistent and non-persistent. In fact, RAS defaults to a persistent assignment by default. This doesn't mean the workload is persistent, just the assignment. These assignments can be released on a schedule to align with a true "pooled" methodology.
-  Remote PC capability - similar to that of Citrix Remote PC. They even have simple onboarding for users and a level of device management for endpoints.
-  RAS has its own equivalent of Machine Creation Services. It offers an automated approach with full sysprep, or it has its own funky RASPrep which achieves the same thing in way less time. When a machine is cloned, it is prepared and added to a pool of resources for sessions to launch from. Simples. AutoScale steps in and dynamically provisions and de-provisions workloads based on your defined thresholds.

[![RAS Prep]({{site.baseurl}}/assets/img/what-in-the-ras/ras_prep.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/what-in-the-ras/ras_prep.png)

Sticking with the provisioning side of things, RAS supports both MAK and KMS activation of hosts.

[![RAS KMS]({{site.baseurl}}/assets/img/what-in-the-ras/ras_kmsmak.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/what-in-the-ras/ras_kmsmak.png)

They also have on-provision optimization (the usual optimizer suspects at play here - remove the crud from the images), and they also have the ability to both deploy and configure FSLogix Profiles within the console.

[![RAS Opt]({{site.baseurl}}/assets/img/what-in-the-ras/ras_opt.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/what-in-the-ras/ras_opt.png)

## Authentication and Identity

Things start to get really funky here. RAS and Identity is pretty impressive. Usual Active Directory low-hanging fruit, but also native MFA integration with a long list of providers. To top it off, they of course have modern SAML authentication, and they even have their own Federated Authentication Services (FAS) equivalent. All onboard. Same console, ready to go.

[![RAS auth]({{site.baseurl}}/assets/img/what-in-the-ras/ras_auth.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/what-in-the-ras/ras_auth.png)

RAS and auth is crazy flexible, you can write all sorts of different rules, route users to different providers and block/allow based on endpoint posture. Again, all onboard, all ready to go.

The last part of this little "holy crap" moment is the native integration with Let's Encrypt. RAS lets you, from the same console as everything else (you get the theme here right…) integrate, automate and leverage lets encrypt SSL certificates including auto-renewal. Yep.

[![RAS lets Encrypt]({{site.baseurl}}/assets/img/what-in-the-ras/ras_letsencrypt.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/what-in-the-ras/ras_letsencrypt.png)

## Policy Control and Customisation

As you can probably guess at this point, the gifts keep coming. The policy control here is extremely fine-grained with almost every aspect of the environment tuneable and configurable. Policies can be fine-grained and applied based on users, groups, computers, gateways, client-based operating systems, IP addresses etc.

[![RAS Policy]({{site.baseurl}}/assets/img/what-in-the-ras/ras_policy.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/what-in-the-ras/ras_policy.png)

All the usual controls and more are there, I can't really find anything lacking and the list is long. Additionally, you can customise the client experience and provide different branding for different groups of users or customers.

## Universal Printing and Scanning

Ever played with Citrix Universal Print Server? Its job is to basically remove driver complexity and reduce complexity. RAS Universal printing does the same thing - onboard.

Universal Scanning follows the same concept - two important concepts in the world of RAS which are made quite simple.

## Session Management and Monitoring

Citrix has Director which is a fantastic tool, RAS offers a similar sort of insight and visibility via (you guessed it) the same console as everything else. All the usual metrics are there; logon details, analysis of logon components, network and session performance, policies, etc etc.

Admins can converse with their users, they can boot them off, shadow and manage sessions as needed. For advanced reporting, RAS integrates with Microsoft SQL SSRS, this is literally the only component that leaves the RAS ecosystem from what I can see, so far, everything we have spoken about is onboard.

RAS monitors itself. Yep. It gives you metrics into the basic performance of each component, and how things are playing. Everything is a click away and easy to find. It even draws a nice picture of itself to show you how the components fit together.

[![RAS monitor]({{site.baseurl}}/assets/img/what-in-the-ras/ras_monitor.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/what-in-the-ras/ras_monitor.png)

As mentioned previously, there are advanced session metrics that are captured and reported on using Influx DB, Telegraf and Grafana, and there is a separate installer to deploy and configure this. I am still classifying this as onboard given it auto-integrates with the RAS console - it's line ball, but it makes the cut on integration.

## Protocol?

Without a dedicated protocol, RAS leverages RDP across the board. RDP isn't bad, but it is very limited compared to that of ICA/HDX/Blast etc. If you don't need all the glory that comes with a more advanced protocol for remoting, then RDP is what it is. They do layer some advanced functionality over the top to make **RDP** *less* **RDP** to be fair, but there is nothing world-shattering.

Not much else to say here **shrugs**.

## Azure Virtual Desktop Integration

This one is big. Very big. RAS is flying under the radar here big time with what they bring to AVD. I have been known to be extraordinarily vocal on the "*We make AVD better*" front for anyone and everything that tries to claim they integrate with AVD (and really don't) but RAS, however, does exactly this.

They automate the deployment of your AVD resources. They can help with initial image builds, Gold image management, application deployment, MSIX app attach, Autoscale, Short Path and cost savings with disk maintenance. And they do this under the same licencing structure we started with - it's just another feature in the bag.

[![RAS AVD]({{site.baseurl}}/assets/img/what-in-the-ras/ras_avd.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/what-in-the-ras/ras_avd.png)

They are also incredibly clever with how they make all of this work. Long and short, connections to AVD via RAS still go via AVD control planes. It's either the native AVD client or a more advanced set of features layered on top (Uni Scanning and Printing etc). But the rest is AVD at the core, just made "*better*" by RAS. Well played, I have no biff here.

## Summary. For now

Embarrassed and impressed. I get the feeling this would be a similar sentiment for anyone discovering RAS for the first time and realising what it can do.

RAS is so far ahead of where I expected them to be, and so far my dealings with the team have been sensational. There is no complexity, they strive for simplicity and the price point is pretty impressive.

I feel like I have scratched the surface of this product and its capability. So far the [documentation](https://download.parallels.com/ras/v19/docs/en_US/Parallels-RAS-19-Administrators-Guide/index.htm) is exceptional, support has been great, and the [tech bytes](https://www.parallels.com/blogs/ras/tech-bytes-intro/) video series is handy. Importantly, the tech does what it should do.

Given the change in strategy and focus for Citrix, the unknowns for VMware, the brutal lock-in of AVD into a single platform and the lack of multi-session support in some other DaaS providers, RAS seems to be in a sweet position for that mid-size customer who is looking for basic remoting capabilities (Citrix, for example, is far more than just a remoting platform and adds capability and value all over the place).

The core of this product definitely stems from RDSH and making that not suck, so it will be interesting to see what happens with Microsoft and their multi-session server OS strategy in the future, but this is something everyone is waiting for with bated breath. They are protecting themselves enough with VDI and AVD integration along with multi-hypervisor support. Well played.

Its biggest challenge from what I can see right now is that it's lacking in its own protocol and associated optimization capabilities for Microsoft Teams, Zoom, etc. There is some redirection capability in the mix, but those focused optimization packs that the big players have aren't there right now. Does everyone need them? Absolutely not, but many do.

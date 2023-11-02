---
layout: post
title: "Enhancing Monitoring and Visibility with Free ControlUp Tools"
permalink: "/enhancing-monitoring-and-visibility-with-free-control-up-tools/"
description: "A collection of free ControlUp tools"
categories: [Citrix, ControlUp, Monitoring]
redirect_from: 
    - /2017/09/25/enhancing-monitoring-and-visibility-with-free-control-up-tools
    - /2017/09/25/enhancing-monitoring-and-visibility-with-free-control-up-tools/
image:
  path: /assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Edit Post Switch to draft Preview Update Editing code Exit code editor Enhancing Monitoring and Visibility with Free ControlUp Tools Type text or HTML Throughout my time deploying Citrix Solutions, I have always been looking at tools that I can keep in my proverbial belt to tackle problems quickly, or provide added value to projects at minimal cost. One of the traditional struggle points I have is the lack of insight into either existing environments, or at times new environments due to the lack of Citrix based monitoring solutions in play, this tends to lead to a guessing game straight off the bat, which is something that drives me up the wall.

ControlUp have been increasingly more friendly in this space and provide a nice selection of tools that all Citrix admins/Consultants should have ready to call on to address some basic everyday requirements. Best thing about it….these tools are all free.

Before delving into the below list, I just want to point out that I have no relationship with ControlUp outside of being a huge advocate of their solutions, the paid and the free. If you want what is in my opinion, the best Citrix and surrounding infrastructure based monitoring solution, then take a look at the Control Up Suite here: [ControlUp](https://www.controlup.com/products/controlup/).

For a while now I have been utilising ControlUp on the UAT phases of my projects to get a clear understanding of what is required and be able to clearly size, based on actual figures and usage stats, how big XenApp Workers or VDI machines should be. Often this leads to a purchase as once people see the visibility they can get, they don’t want to let it go.

Conversely, UberAgent offers a very similar solution which is particularly insightful around understanding existing environments. A benefit of UberAgent is that a free consultant licence is available, which ties straight in to a free trial of Splunk. The downfall in my opinion, is purely the limitation around Splunk and the amount of data you can consume in a trial. The same limitations don’t apply with ControlUp, however you get a significantly smaller trial period, but realistically, for a sizing only exercise, you should be able to do a lot in 21 days.

## NetScaler Monitor - Free

NetScaler to many admins, perform a whole lot of Dark Magic to do some pretty amazing things. Many Citrix Admins will deploy NetScaler as part of a XenApp and XenDesktop solution, and may not touch the configuration in months, if not years in some instances. There are not many solutions out there yet that are providing in-depth monitoring into the Citrix NetScaler, and even less that I am aware of that are integrating this into the overall monitoring solution provided (stay tuned on Control Up v7.1).

The NetScaler Monitor from ControlUp is a free tool which will provide you some excellent insight into your existing NetScaler deployment. It’s a small install consisting of two parts. A collector component which reads the data, and a web based management portal in which you can add, view, and drive certain features within your NetScaler deployment in almost real-time.

The monitor can be installed on any server in the environment, and can be accessed via standard web browsers by multiple admins

[![NetScalerMonitor]({{site.baseurl}}/assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/NetScalerMonitor.png)]({{site.baseurl}}/assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/NetScalerMonitor.png)

This is a free offering. Remarkable in my opinion [Free NetScaler Monitor](https://www.controlup.com/blog/free-powerful-netscaler-monitor/). There is a neat walk through video here [NetScaler Monitoring Walk Through](https://www.controlup.com/controlup-netscaler/)

## Application Load Time Profiler- Free

The Application Load Time Profiler is a free tool that measures the time it takes any Windows application to load and provides a deep visual analysis allowing you to understand the reasons causing an application to load slowly. Pretty cool to be able to delve into any application and understand the effect it has on the environment, and also drill down to see environmental issues that may be effecting the application such as IO limitations etc

[![AppProfilerGimp]({{site.baseurl}}/assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/AppProfilerGimp.png)]({{site.baseurl}}/assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/AppProfilerGimp.png)

[![AppProfilerGimpPost]({{site.baseurl}}/assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/AppProfilerGimpPost.png)]({{site.baseurl}}/assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/AppProfilerGimpPost.png)

The Application Load Time Profiler tool can be downloaded here: [ControlUp Application Profiler](https://www.controlup.com/blog/controlups-application-profiler-free-tool/)

## Logon Simulator - Free

The ControlUp Logon Simulator rocks. I wrote a post a while ago on how you can use this tool to Warm Up Images to prevent first user logon slowness [here](https://jkindon.wordpress.com/2017/08/27/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/)... however the actual goal of this tool was to provide admins or consultants with a tool to simulate user logons using synthetic sessions, as well as to test the availability & responsiveness of all elements taking part in real users login in your Citrix Environment

[![ControlUpMain]({{site.baseurl}}/assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/ControlUpMain.jpg)]({{site.baseurl}}/assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/ControlUpMain.jpg)

The Control Up Logon Simulator communicates directly with the Citrix Storefront store and tests the following:

-  Authentication
-  Receiver traffic flows
-  Storefront services
-  Application enumeration
-  Broker availability
-  Application launch

Throughout these tests, the simulator tracks metrics such as connection time and logon time allowing you to track and trend changes over time as required.

The tool is available for free here: [ControlUp Logon Simulator](https://www.controlup.com/controlup-logon-simulator/) and the admin guide can be found here: [ControlUp Logon Sim Admin Guide](https://www.controlup.com/wp-content/themes/porto/assets/files/Logon_Simulator_Guide.pdf)

## Logon Analysis Scripts - Free

There are a couple of very handy scripts from ControlUp which can be really great for troubleshooting the logon process and identifying problematic Group Policies.

The first script breaks down each Client Side Extension component, and tells you how long it takes to run - this is great for finding things like printers that can't be found, drive mapping which are wrong etc. These are often very easy to fix yet can have a terrible effect on the end users logon process. The below script is a must have [Analyse CSE Extensions](https://www.controlup.com/blog/logon-gpo-analysis-via-powershell/)

[![GetCSE]({{site.baseurl}}/assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/GetCSE.png)]({{site.baseurl}}/assets/img/enhancing-monitoring-and-visibility-with-free-control-up-tools/GetCSE.png)

The other script I like is that Analyze Logon Duration script from ControlUp, This will break down the components of a login process and help identify things like profile management or Group Policies causing delays in login times. [Analyze Logon Duration](https://www.controlup.com/analyze-logon-duration/)

Please note, at this point in time, this script is not operational as a standalone script against Windows Server 2016 - hopefully ControlUp can re-release this soon

## In Closing (for now)

What I really like about this company and why I wanted to share this with a focus on them (there are other cool tools I will share in the future) is that they have a focus on community and sharing - which is pretty awesome given the amount of time that would have gone into these tools.... so thanks ControlUp!

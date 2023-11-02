---
layout: post
title: "Image Optimization Analysis – Citrix XenApp"
permalink: "/image-optimization-analysis-citrix-xenapp/"
description: "Diving into the impacts of image optimizations"
categories: [BIS-F, Citrix, ControlUp, Optimization, XenApp]
redirect_from: 
    - /2017/09/02/image-optimization-analysis-citrix-xenapp
    - /2017/09/02/image-optimization-analysis-citrix-xenapp/
image:
  path: /assets/img/image-optimization-analysis-citrix-xenapp/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

It's well known and hopefully understood these days, that any standard out of the box Windows build is going to be full of fat, and probably not ideal for Citrix XenApp or XenDesktop deployments. Not to say it won’t work, just that there are a lot of things that don’t need to be there and have a proven adverse effect on Citrix deployments.

There are many different flavours and options around what to optimize, who's scripts to run, what does what, when to use who, and why use anything at all, so I decided to try and quantify with some numbers, the common tools available for these tasks, where they cross over, and any considerations.

Important to note, that this post does not discuss provisioning, underlying hardware, cloud platforms or any of the infrastructure based optimizations that can be implemented. I am focusing on nothing but the Operating System, and running these measurements in my home lab (though I also deploy these on my projects). My environment is not provisioned in any way, purposely to keep things on an even playing ground. No magical caching, no PVS benefits/Negatives, no MCS IO etc. The results I see are with just raw OS and Opt.

I also, by design, have next to no Group Policies defined in my environment outside of Policies to drive profile management, folder redirection, basic security and environment configurations. All user customisations are driven by WEM and my testing does include some basic drive maps, registry key imports etc. This post focuses on OS optimizations for hosted shared environments, though many of the same would apply to a windows 10 VDI deployment.

The build is a vanilla install of Windows Server 2016 Data Centre Edition, with Citrix VDA 7.15 LTSR inclusive of Citrix User Profile Management and Citrix WEM Agent 4.4. This is the baseline build for all of our work. All measurements are based on second launch, as per my previous post, [first launch is never fun](https://jkindon.wordpress.com/2017/08/27/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/). I am using the Control Up Logon Simulator to do all session launches and providing measurements based off its findings. My own highly technical stop watch test mirrors these results.

## The grunt work - Optimization testing

### Standard Windows Server 2016 - No Optimization

As the title suggests, this is a stock standard install of Server 2016 with the VDA 7.15 LTSR installed. I removed windows defender because it makes me sad

Test 1. A standard user account, launching a published desktop:

Logon Figures with WEM driving, however no image optimization

[![initialanalysis]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_opt_test1_logon_2016.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_opt_test1_logon_2016.jpg)

A little bit of a chicken before the egg moment here, but it's interesting to note that at the very beginning of this, with a vanilla build, the VMWare Optimization tool believes that there are 94 optimizations recommended and not applied, 30 applied without anything being configured other than disabling windows defender etc on my template. What's even more interesting here Is that I have created my own Optimization template based on the Server 2016 RDSH recommendations and removed anything HKCU and Hyper-V related. More on why this is interesting later.

[![vmwareopt]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/vmware_opt_precheck.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/vmware_opt_precheck.jpg)

### Base Image Script Framework (BIS-F)

The base image script framework is one of my favourite tools. It is a community initiative written by the boys at loginconsultants with the input of many community leaders and it is an absolute no brainer for optimizing and preparing your environment. I won't go deep into what BIS-F does, go and read (and rejoice) here: [BIS-F](https://www.loginconsultants.com/en/news/tech-update/item/base-image-script-framework-bis-f)

[![bisf]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/bisf_running_prod.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/bisf_running_prod.jpg)

Test 2. A standard user account, launching a published desktop, optimized with Base Image Script Framework

[![bisfresults]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_opt_test2_logon_2016_bisf.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_opt_test2_logon_2016_bisf.jpg)

There is a marginal increase in logon times, but nothing to be amazed at. However the image Is "cleaner" (I don’t know how to measure this other than to say it feels snappier)

Considerations: None. Do it. This tool keeps growing and growing. The next release which is already in test mode is going to allow you to drive all of the tools I mention in this post via BIS-F and the list of what It can do keeps growing.

### Citrix Optimizer Tool

The Citrix Optimizer tool is again, a Power Shell driven tool which provides some basic optimization recommendations from the lads (and ladies) over at Citrix. It provides an analysis module to let you compare what it wants, vs what you have. You can find it here: [Citrix Optimizer](https://support.citrix.com/article/CTX224676)

The below shows what Citrix Optimizer looks like on a stock standard image with BIS-F run once. Important to note here that there is a significant amount of services and optimizations that can be enabled straight off the bat according to Citrix.

[![xa_ctxopt_analysis_2016]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_ctxopt_analysis_2016.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_ctxopt_analysis_2016.jpg)

Test 3. A standard user account, launching a published desktop, with both BIS-F and Citrix Optimizer applied

Logon Time BIS-F + Citrix Optimizer and Citrix WEM

[![xa_opt_test3_logon_image1]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_opt_test3_logon_image1.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_opt_test3_logon_image1.jpg)

Considerations: Currently its in Beta, however I have had no issues as yet

### VMware OS Optimization Tool

The VMware optimizer tool is another new player in this space and this tool is more that capable of optimizing a Citrix image on multiple Hypervisors with great success. It also provides an analysis module to let you compare what it wants, vs what you have. You can find the tool here: [VMWare OS Optimization Tool](https://labs.vmware.com/flings/vmware-os-optimization-tool)

Note, I created a custom template, removed all user based optimizations and removed the Hyper-V service removal as I use Hyper-V in my lab and any HKCU optimization is driven via WEM

As I noted above, originally on a Vanilla image, with no optimizations run, the VMWare tool suggested 94 optimizations were still missing. The below scan with the same template was run against my image that had BIS-F and the Citrix Optimization tool run on it, and still, VMWare believe there are 80 more optimizations to be run…

VMWare Analysis against Standard Windows Server 2016 with BIS-F + Citrix Optimizer:

[![xa_vmwareopt_analysis_2016_bisf_ctxopt]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_vmwareopt_analysis_2016_bisf_ctxopt.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_vmwareopt_analysis_2016_bisf_ctxopt.jpg)

Test 4. A standard user account, launching a published desktop, VMware Optimizer, BIS-F, Citrix Optimizer

Logon Time with BIS-F, Citrix Optimizer, Citrix WEM and VMware Optimizer Combined

[![xa_opt_test4_logon_2016_bisf_ctxopt_vmopt]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_opt_test4_logon_2016_bisf_ctxopt_vmopt.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/xa_opt_test4_logon_2016_bisf_ctxopt_vmopt.jpg)

Considerations: The tool is currently in technical preview. You should also be extremely sure of the optimizations it will apply. If you run Hyper-V, the VMware tool will (no surprisingly) kill Hyper-V services etc. Not much fun if you run it blindly, but the same can be said about any of these tools.

### Summary of System Optimization Tools

The above tools all have a focus on optimizing the underlying Operating System specifically for a Citrix based Shared Hosted environment. All of these tools have Templates available for Windows 10 VDI environments as well, and BIS-F should be run on anything you plan on provisioning in my opinion.

Surprisingly, though I guess it shouldn’t be given the focus, was that I noticed barely any difference in logon times at all across these Optimizations. I did however notice that there was a massive difference in the general feel of the image, and my resource consumption was well down on default when machines were idle. There are few factors here relating to this finding:

-  My Lab doesn’t look like a production environment. The measurements I display above aren't necessarily achievable in the outside world, however I have run these tools across production environments and well and truly guarantee that you will increase user experience as a whole. This post isn't about the times I am displaying, it's about the tools
-  I don’t have a significant amount of Group Policies and I have zero preferences. I configure everything in WEM
-  I have no provisioning. Provisioning will instantly throw up your logon times, however I have worked on an environment recently to optimize the environment with Citrix UPM and WEM. The environment was running on a Nutanix backend, and with the introduction of WEM and UPM, with MCS based provisioning on Server 2012 R2 I had consistent sub 7 second logons for the full office suite etc.
-  I believe my UPM configurations are optimal based on trial and error and the hard work done by others that I get to ride off the back of

[![summary_logonfindgs]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/summary_logonfindgs.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/summary_logonfindgs.jpg)

[![testtesttest]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/testtesttest.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/testtesttest.jpg)

It would be completely remiss of me not to point out that you should of course, test the living daylights out of this before putting anything into production - but don't be daunted by the need to test - the gains are unreal.

## Logon Performance Management and Optimizations:

There are a few tools and tricks that I swear by in any deployment which do directly relate to the users logon and session experience.

### Optimized Profile Management

Optimized profile management is step 1 in any of this work. Configure your UPM properly, and you will be in a happy place. As a side note, if you deploy FSLogix Profile Containers, you will be in an even happier place and wont really have to do anything - awesome!

I owe any UPM work I do to these two fellas, Carl Stalhood: [Citrix Profile Management](http://www.carlstalhood.com/citrix-profile-management/) and James Rankin: [Profile Management](http://www.htguk.com/everything-you-wanted-to-know-about_26/)

### Group Policy Loopback

I am sure this will raise a few eyebrows as there are lots of Microsoft posts and guidance around using Loopback in replace mode which will contradict what I am saying, but based on experience time and time again, the first thing I do on any citrix deployment, is create a new OU, block inheritance, create a new GPO and enable Loopback processing in replace mode. The most painful thing when trying to track down why logons are painful, is when you aren't in control of what is being applied to your images.

Want to really understand how narky these policies and preferences can be, have a look at this script by the Control Up lads to show you the actual time taken by preferences and the individual CSE's (Client Side Extensions) here: [Logon GPO Analysis](https://www.controlup.com/blog/logon-gpo-analysis-via-powershell)

Carl Webster also has a great all round GPO processing time Script here: [Carl Webster - Get GPO Time](http://carlwebster.com/downloads/download-info/get-gpo-processing-time-xenapp-6-5/)

[![webstergpo]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/webstergpo.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/webstergpo.jpg)

Spend the time, recreate policies as required, move preferences into WEM, and manage your VDI or RDSH environment as a separate beast. It is not the same as your desktop environment, it may need to look the same, but it should not be governed the same policies and settings that drive the rest of your desktop fleet. Treat the environment with some love and it will repay you, let the other admins apply ridiculous printer settings in user preferences and knock out the desktop logon times, and watch your VDI/RDSH environment stay happy

### Workspace Environment Manager

If you haven't been across what WEM is, there is a good chance you have been hiding under a rock, or just too busy to realise how awesome this acquisition was by Citrix. This technology categorically changes the way we deal with XenApp Shared Hosted environments, as well as VDI deployments. It is simply genius and the results are astonishing

If you want to get excited about a technology that is now available to Enterprise and Platinum Customers at no cost, read this post by Hal Lange: [Welcome to Citrix Workspace Environment Manager](http://www.thinclient.net/blog/?p=755)

-  George Spiers: [Workspace Environment Manager](http://www.jgspiers.com/citrix-workspace-environment-manager/)
-  Carl Stalhood: [Workspace Environment Manager](http://www.carlstalhood.com/workspace-environment-management/)
-  Citrix Docs: [E-Docs WEM](http://docs.citrix.com/en-us/workspace-environment-management/current-release.html)

I will warn you, this becomes addictive. The more you can rip out of GPO preferences and throw into WEM, the more your users will buy you scotch at Christmas (or simply not yell as much which is about as rewarding).

[![wem]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/wem.jpg)]({{site.baseurl}}/assets/img/image-optimization-analysis-citrix-xenapp/wem.jpg)

Even if you don’t have the time to start moving your user environment configuration into WEM, at least install it and enable its resource Optimization technology. Its instant wins for your organisation

### Internet Explorer Easy List

This little beauty is one that has been identified a few times on the web as a huge CPU saver in Shared Environments, however I think the best detail and information on how it works and how to configure it is done by James Rankin: [Easy List - Improving XenApp CPU Performance](http://www.htguk.com/improving-citrix-xenapp-session/)

This is makes a rather impressive difference to your server density, and combined with WEM (Everything in life should be combined with WEM) completely changes the profile or your server utilisation (Control Up will categorically prove this if you don’t believe me)

### George Spiers Optimization Work

When we start talking user Logon optimizations, there are a few more things to think about, George Spiers has documented some neat tricks to help with the logon times of users, and making Director a bit more relevant to the everyday admin here: [Reduce Logon Times by Up to 75%](http://www.jgspiers.com/citrix-director-reduce-logon-times/)

## Summary

To make sure I wasn’t missing a key component, I went nuts with applications installs on my image (well I didn’t, I made chocolatey go and do it for me) and I ran through about 50 different applications, everything from Java, Adobe Reader, VLC, Quicktime, LibreOffice, PDF Printers, Chrome, Firefox, VSCode, Paint.Net, Notepad++ just to name a few (I seriously went to town). What was impressive was that another run of BIS-F to clean things up, and there was zero impact on my logon times (I also did a few of the bits above). My image was just as snappy as when i had nothing on it

My takings from this little experiment and my last 12 months of really focusing on environment optimizations, is that the Citrix world (and RDSH as a whole) is an ever changing environment with a stupid amount of moving parts. We are extremely lucky to have such awesome community driven contributions that provide us a way to do things we would never have the time (or inclination) to do ourselves. With the shift towards Cloud Compute and the cost implications that force us to look at density figures etc, its madness to not at least be looking at some of the above tools and figuring where they fit in your environment

Update 19.12.17: [Dennis Span](http://dennisspan.com/image-optimization-tools-comparison-matrix/) and [Chris Twiest](https://workspace-guru.com/2017/11/07/optimizers-vmware-vs-citrix/) both have awesome articles going into more depth around these tools - both are must reads
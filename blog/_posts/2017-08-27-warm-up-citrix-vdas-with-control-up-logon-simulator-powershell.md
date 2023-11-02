---
layout: post
title: "Warm up Citrix VDA’s with ControlUp Logon Simulator and PowerShell"
permalink: "/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/"
description: "Taking the pain out of the first logon with ControlUp Logon Sim and PowerShell"
categories: [Citrix, ControlUp, PowerShell]
redirect_from: 
    - /2017/08/27/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell
    - /2017/08/27/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/
image:
  path: /assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

For a while now I have been a huge advocate of the logon optimisation work that George Spiers has provided, and I have been testing and implementing the majority of these optimisations on the projects I am involved with. Its led a to a passion (obsession) around reducing logon times and optimising the user experience, and the results have been telling. Technologies like Citrix Workspace Environment Manager, combined with many other small optimisations completely change what an out of the box Citrix Deployment looks and performs like

You can read more about Georges work [here](http://www.jgspiers.com/)

One existing annoyance that I have, is that the first logon for a user in most environments is still slow despite the many optimisations that we can put in place. Sure, there are some options around auto logons (interactive logons) or even scripted RDP Logons, to try and knock out first user logon slowness, but none of these do the same job as launching a pure ICA/HDX based XenApp Session. I have found that the first session of the day, on average can take anywhere between 15-30 seconds longer than the rest depending on the environment. Would be nice if we could remove this…

Enter Control Up Logon Simulator

[![logo-2]({{site.baseurl}}/assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/logo-2.png)]({{site.baseurl}}/assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/logo-2.png)

A little while back, the good people at ControlUp released a free logon simulator which can be used to test end to end connectivity to a Citrix environment. Furthermore, they made it possible for it to be command line driven using an xml launch file…perfect…A little PowerShell later, and I was able to create a tool that does exactly what I need

The logic and use of the tool is as follows

--> Identify a launch machine
This machine can be as simple as a windows 10 Virtual machine. It's doesn’t need to be anything special. Its role is to do nothing but drive Control Up Logon Simulator to launch sessions against the Citrix Environment.

If you have hundreds of VDAs, you may find it makes sense to have multiple Launch Machines

The launch machine will require Citrix Receiver installed

--> **Create an Active Directory User Account to drive the solution**
The entire solution can work off a single Active Directory User Account. I refer to this as the Launch Account. Its job is as follows

-  Auto Logon to the Launch Machine(s)
-  Have access to any published resource you want to launch via Control Up

--> **Utilise Martin Zugec's XA-CreateDesktopsForVDAs PowerShell Script.**
This is one of the most useful scripts around the place. It simply creates a published desktop for every server capable of shared hosted sessions within your Citrix Environment and ties resource to server via the use of tags. It names them the same as the actual Server Name by default, and assigns an Active Directory Group. Perfect for us when all we want to do is launch an ICA/HDX session against each server
<https://www.citrix.com/blogs/2017/04/17/how-to-assign-desktops-to-specific-servers-in-xenapp-7/>

It makes sense to use the Active Directory Launch Account nested into an Active Directory Group, so create a group and nest your Active Directory Launch Account into it.

--> **Download and Install Control Up Logon Simulator**
This is currently a free tool released by Control Up located here:
<https://www.controlup.com/products/logon-simulator/>

[![ControlUpSettings]({{site.baseurl}}/assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/ControlUpSettings.jpg)]({{site.baseurl}}/assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/ControlUpSettings.jpg)

--> **Download and Install SysInternals Auto Logon to provide an auto logon for the AD Action account**
Download and Install this on the Launch Machine(s). Configure with the Launch Account so that on every reboot, the machine Auto Logs in with your Launch Account, runs a scheduled task to launch the script (which locks the session) and away we go [https://docs.microsoft.com/en-us/sysinternals/downloads/autologon](https://docs.microsoft.com/en-us/sysinternals/downloads/autologon)

--> **Create a scheduled Task with Multiple Triggers**
Launching the script via a scheduled task allows complete flexibility for the environment. We obviously need multiple triggers as a base, one to cater for a machine start-up or reboot, the other to ensure daily repetitions of the warm up tasks, however you may find different triggers that you want to implement depending on your environment

Note in the process below, I have enabled Port testing against 1494 on each VDA prior to launch, to ensure we aren't wasting valuable time attempting to launch resources against a down server. Full logging is enabled so you can track through all requests

[![PowerShellRunning]({{site.baseurl}}/assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/PowerShellRunning.jpg)]({{site.baseurl}}/assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/PowerShellRunning.jpg)

[![ControlUpMain]({{site.baseurl}}/assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/ControlUpMain.jpg)]({{site.baseurl}}/assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/ControlUpMain.jpg)

[![LogFile]({{site.baseurl}}/assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/LogFile.jpg)]({{site.baseurl}}/assets/img/warm-up-citrix-vdas-with-control-up-logon-simulator-powershell/LogFile.jpg)

The code for this as always, is open and available on my [github page](https://github.com/JamesKindon/VDAWarmUp), along with any other tools and scripts I have and will develop.

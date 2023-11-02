---
layout: post
title: Conquering Per User Firewall Rules with Citrix WEM Privilege Elevation
permalink: "/conquering-per-user-firewall-rules-with-citrix-wem-privilege-elevation/"
description: Conquering Per User Firewall Rules with Citrix WEM Privilege Elevation
categories: [Citrix, WEM, Teams, Security, CVAD, Cloud, Privilige Elevation]
redirect_from: 
    - /2021/05/31/conquering-per-user-firewall-rules-with-citrix-wem-privilege-elevation
    - /2021/05/31/conquering-per-user-firewall-rules-with-citrix-wem-privilege-elevation/
image:
  path: /assets/img/conquering-per-user-firewall-rules-with-citrix-wem-privilege-elevation/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

I recently worked with a customer who tested deployment of Microsoft Teams on Citrix without any form of optimization. The optimization packs were hindering user acceptance and not providing adequate stability. This post is not about the why of Teams optimization or not, but rather than tactics used to tackle a side requirement – per user firewall rules on non-persistent desktops with no admin rights. A note of thanks to the team (you know who you are) that was patient enough to test these features and work through the challenges.

## The Challenge

The initial challenge presented itself after switching to the per user installation of Teams. When launching Teams, all appears fine, until you try to access devices, be it via making a call or by accessing settings -> devices. This would prompt for the creation of a defender firewall rule as documented [here by Microsoft](https://docs.microsoft.com/en-us/microsoftteams/get-clients#windows). Telling the users to hit “cancel” and move on is really not acceptable, particularly as this would happen repeatedly in a non-persistent environment.

Microsoft offer a fairly half-baked solution [here](https://docs.microsoft.com/en-us/microsoftteams/get-clients#sample-powershell-script---inbound-firewall-rule) which is basically a PowerShell script to look at a list of user profiles in the system drive, and write the firewall rules based on its findings. Again, half-baked and not scalable in a non-persistent environment.

Typically, with Firewall rules, we can centrally configure them with a GPO, however for some reason unbeknownst to me, Microsoft do not allow for wildcards or user based variables in Firewall paths so "***C:\users\James\AppData\Local\Microsoft\Teams\Current\Teams.exe***" suddenly becomes a bit of a challenge to manage.

## The Requirements and The Solution

What we needed, is a solution that could do several things:

-  Create the firewall rule in the user context silently
-  Not require users to have admin rights
-  Handle non-persistent environment

What we used, was a combination of tools. Note, this is a Citrix Cloud environment referenced here, so the WEM Service was available for consumption.

## Citrix WEM Service Privilege Elevation

The Citrix Workspace Environment Management Service offers part of the solution in the form of [Privilege Elevation](https://docs.citrix.com/en-us/workspace-environment-management/service/user-interface-description/security.html#privilege-elevation). Privilege elevation allows us to elevate an executable in the user context without providing the user administrative privileges. Promising, but has a few considerations:

-  WEM can only elevate executables at the time of writing, not PowerShell
-  WEM supports only elevating executables that are stored locally to the agent. Whilst I was successful in elevation from a NAS device in my own environment, I struggled with an Azure NetApp Files share, and the support stance from the WEM team is that executable must be local
-  Elevation ONLY occurs in the context of Windows Explorer, trying to leverage a script, or external task to execute the exe for the user will not trigger elevation

## PowerShell Script

I tweaked the PowerShell example from Microsoft to make it per user based, with the logic that we would execute the code on logon, and tackle the non-persistent requirements that way. Sounds good in theory, except WEM can’t elevate PowerShell (yet) and as such, I needed to convert my script to an exe.

I am no genius with this stuff, Ps2exe was an absolute breeze to use, and it was [native to the PowerShell Gallery](https://www.powershellgallery.com/packages/ps2exe/1.0.10) so job done. No doubt some environments will struggle with AV solutions that will get upset with this, but Defender was fine with it. The script is [located here](https://github.com/JamesKindon/Citrix/blob/master/TeamsPerUserFirewall.ps1).

```powershell
Install-Module ps2exe
Invoke-PS2EXE .\TeamsPerUserFirewall.ps1 .\TeamsPerUserFirewall.exe -noConsole -noOutput
```

## Localisation Of The Files

Given that we couldn’t elevate from a network location, we put together a small folder structure locally, we have different requirements for different users, so we wanted a fine grained level of control with what we allowed WEM to elevate and for who. So it was simple. Store the executables on a network share, and use Group Policy Preferences in the machine context to bring the files locally

**Source**: `\\Network\Share\TeamsPerUserFirewall.exe`

**Destination**: `C:\Temp\ApplicationInstalls\Teams\TeamsPerUserFirewall.exe`

## Execution Of The Solution

This was a touch painful and still gives me a few heebie jeebie moments as it leads back to execution of tasks in a way I usually strip out, but it is what is it is. As mentioned, WEM can only elevate in the context windows explorer, so we need the execution to occur as if the user was double clicking the exe. I found two ways of doing this:

-  Using the Windows Start Menu "Startup" folder.
-  Using the legacy run list in the registry HKLM\Software\Microsoft\Windows\Current Version\Run (Thanks James Rankin for the nudge)

Choose your poison, but be warned that some environments will disable the execution of applications from the legacy run list.

## Putting It All Together

This is how the final solution looks:

-  We have a PowerShell script compiled as an executable copied to the local device on start up (GPO)
-  We have a WEM elevation rule that elevates the executable based on the local path and user group membership

[![WEM Elevation Rule]({{site.baseurl}}/assets/img/conquering-per-user-firewall-rules-with-citrix-wem-privilege-elevation/Rule.png)]({{site.baseurl}}/assets/img/conquering-per-user-firewall-rules-with-citrix-wem-privilege-elevation/Rule.png)

-  We have used Group Policy to write the HKLM\Software\Microsoft\Windows\Current Version\Run key to execute the executable on user logon which writes the firewall rule

## Troubleshooting WEM Privilege Elevation

There was a bit of learning with WEM elevation here, a few notes from the field:

-  The solution is in it’s first iteration, so there is a lot of potential for improvement
-  Logging is key. All elevation logging is logged to the user profile by WEM. The layout of the log files appears to need some work
-  Logs are uploaded to the WEM central admin console, however this is not immediate, so you need to allow time for data upload to occur before you can review the results in the console. Local logs are of course real-time. I believe the logic is a 1-hour delay on the first log upload, and the incremental uploads every 1 minute after the first, however I didn’t experience consistency in the 1-minute increments

[![WEM Central Logging]({{site.baseurl}}/assets/img/conquering-per-user-firewall-rules-with-citrix-wem-privilege-elevation/Logs.png)]({{site.baseurl}}/assets/img/conquering-per-user-firewall-rules-with-citrix-wem-privilege-elevation/Logs.png)

-  WEM elevation will fail if the “deny log on as a batch job” security policy is altered. WEM elevates using batch logons. You should also be cautious of the “Allow log on locally” policy alterations that often occur
-  Do not turn on the “Enforce RunAsInvoker” setting in Citrix WEM unless you want to kick your own backside. This is of course following the principle of “least privilege” but it is a right prat when you are trying to work with basic Microsoft environments and consoles. You have been warned

[![WEM Central Logging]({{site.baseurl}}/assets/img/conquering-per-user-firewall-rules-with-citrix-wem-privilege-elevation/RunAsInvoker.png)]({{site.baseurl}}/assets/img/conquering-per-user-firewall-rules-with-citrix-wem-privilege-elevation/RunAsInvoker.png)

## Summary and Thoughts

Again, this post is not focused solely on Teams, we tackle the same requirement/challenge with different applications using this approach successfully. I wanted to avoid disabling Windows Firewall and I wanted a graceful non-user impacting solution to address the challenge. We also had requirements for additional per-user installations of software that required administrative rights, so it was great timing for the release of the WEM privilege elevation feature

There are no doubt additional ways of tackling the challenges, and no doubt it’s not going to work in all environments, but it does the job so far. Privilege elevation will mature with Citrix, and heaven forbid, Microsoft might even one day all the basic concept a user variable in a firewall path, and this entire process will be defunct, but for now, it does the job gracefully.

I tested this with Windows 10 Multi-Session and Windows Server 2019.
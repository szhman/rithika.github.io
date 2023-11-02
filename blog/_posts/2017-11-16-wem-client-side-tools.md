---
layout: post
title: "WEM Client Side Tools"
permalink: "/wem-client-side-tools/"
description: "Leveraging the client side tools for Citrix WEM"
categories: [Citrix, WEM]
redirect_from: 
    - /2017/11/16/wem-client-side-tools
    - /2017/11/16/wem-client-side-tools/
image:
  path: /assets/img/wem-client-side-tools/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

This post aims to provide some insight into the available tools to help with troubleshooting Citrix Workspace Environment Management and making the most of some cool features available that are semi hidden or not yet advertised heavily

## WEM Agent Log Parser

Path: `C:\Program Files (x86)\Norskale\Norskale Agent Host\Agent Log Parser.exe`

[![AgentLogParser2]({{site.baseurl}}/assets/img/wem-client-side-tools/AgentLogParser2.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/AgentLogParser2.png)

WEM Agent Log Parser
{:.figcaption}

The log parser is my go to tool for any client side troubleshooting. This provides a nice easy GUI to easily navigate through the WEM logs (Which are created under the users profile %UserProfile%) and provides the ability to drill into multiple different facets of the log file, such as individual Actions, or control functions such as environment settings (Known as Main Controller log entries).

Citrix Doco defines this tool as:

> The WEM Agent Log Parser allows you to open any Workspace Environment Management agent log file, making them searchable and filterable. The parser also summarizes the total number of events, warnings and exceptions (in the top right of the ribbon), as well as details about the log file (the name and port of the infrastructure service it first connected to, as well as the agent version and username)

[![AgentLogParser1]({{site.baseurl}}/assets/img/wem-client-side-tools/AgentLogParser1.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/AgentLogParser1.png)

WEM Agent Log Parser - Filtered
{:.figcaption}

This is great for seeing exactly what has been processed and why. Or more commonly, why things are not as you might expect. Edocs link to the tool is here: [WEM Log Parser](https://docs.citrix.com/en-us/workspace-environment-management/current-release/reference/log-parser.html)

## Resultant Actions Viewer (RSOP)

Path: `C:\Program Files (x86)\Norskale\Norskale Agent Host\VUEMRSAV.exe`

[![RSOP1]({{site.baseurl}}/assets/img/wem-client-side-tools/RSOP1.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/RSOP1.png)

WEM Resultant Actions Viewer - Applied Actions
{:.figcaption}

This guy is one of the most useful tool for troubleshooting situations where you expect to see something, but do not. This is the equivalent of the good old Windows RSOP (Resultant Set of Policy) Tool we have all grown up with for troubleshooting Group Policy.

This will show you every single thing that is applying to users, and the criteria that dictates why it is applying. It will also show you what has been excluded and why, which is extremely handy when you start delving into complex if/then style logic with WEM.

[![RSOP2]({{site.baseurl}}/assets/img/wem-client-side-tools/RSOP2.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/RSOP2.png)

WEM Resultant Actions Viewer - Excluded Actions
{:.figcaption}

This tool is the client side equivalent of the server Actions modelling wizard, however the server side tools projects what should be, the client side Actions viewer tells you what is

[![RSOP3-SS]({{site.baseurl}}/assets/img/wem-client-side-tools/RSOP3-SS.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/RSOP3-SS.png)

WEM Admin Console Modeling Wizard
{:.figcaption}

We can also view environmental settings, Microsoft USV Settings, Agent Settings, User Group memberships (good for tracking expected actions that aren't there…) and it also has another inbuilt log viewer which defaults to reading the `Citrix WEM Agent` under the user profile `%UserProfile%\Citrix WEM Agent.log`.

A neat trick here is that you can enable a debug mode on the fly without having to go through and fully enable debug logging for your site (Thanks Hal Lange for the insight on that one)

[![RSOP4]({{site.baseurl}}/assets/img/wem-client-side-tools/RSOP4.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/RSOP4.png)

WEM Resultant Actions Viewer
{:.figcaption}

## VUEMAppCMD

Path: `C:\Program Files (x86)\Norskale\Norskale Agent Host\VUEMAppCmd.exe`

![AppCMD](https://jkindon.files.wordpress.com/2017/11/appcmd.png)

A good tool to integrate your published applications with. The premise of this guy is that we can make sure WEM has completely finished processing an environment before launching a published application. Let's say you needed a set amount of drives, variables and any other environmental setting prior to launching the application (remember WEM processes after desktop load), then you can basically alter your published application to use VUEMAppCMD.exe with your application name as a switch.

Basically just makes the application wait for WEM to finish processing before launching.

## Manage Printers

Path: `C:\Program Files (x86)\Norskale\Norskale Agent Host\PrnsMgmtUtil.exe` 

[![Printers]({{site.baseurl}}/assets/img/wem-client-side-tools/Printers.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/Printers.png)

WEM Printer Management
{:.figcaption}

Neat little utility that can be provided to users to manage their own printers - this is the equivalent of right clicking on the user agent icon and selecting Manage Printers. Handy to know that path in case you want to publish shortcuts to desktops or start menu and you have hidden the WEM agent icon

![Printers2](https://jkindon.files.wordpress.com/2017/11/printers2.png)

## Manage Applications

Path: `C:\Program Files (x86)\Norskale\Norskale Agent Host\AppsMgmtUtil.exe`

[![Applications1]({{site.baseurl}}/assets/img/wem-client-side-tools/Applications1.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/Applications1.png)

WEM Application Management
{:.figcaption}

This can be a nice way of making applications available to users, but allowing them to choose how and what applications are stored where from a shortcuts perspective - goes hand in hand with providing a set of systems, but not necessarily dictating how they are presented to users.

This is the equivalent of right clicking on the user agent icon and selecting Manage Applications

[![Applications2]({{site.baseurl}}/assets/img/wem-client-side-tools/Applications2.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/Applications2.png)

Just be sure on any of the above (Printers, Applications etc) that you enable user Interaction within WEM (or block it if you see fit)

[![AppsAllow]({{site.baseurl}}/assets/img/wem-client-side-tools/AppsAllow.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/AppsAllow.png)

WEM User Interaction Settings
{:.figcaption}

## Help Desk Tools

WEM has a funny little feature aimed at Help Desktop Situations. Its actually a cool little tool to allow your users to take an on the fly screen dump of an issue, and have it send directly to a predefined support email address.

Right clicking the WEM Agent Icon and selecting "capture Screen" presents the user with an easy to drive screen capture tool as per below. Clicking send to support will call on predefined WEM settings that you define within the Admin Console

[![HelpDesk1]({{site.baseurl}}/assets/img/wem-client-side-tools/HelpDesk1.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/HelpDesk1.png)

 WEM Capture Screen
{:.figcaption}

[![HelpDesk2]({{site.baseurl}}/assets/img/wem-client-side-tools/HelpDesk2.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/HelpDesk2.png)

WEM Helpdesk Options
{:.figcaption}

Interestingly, and I am yet to figure out where you would really use this, there are 2 "Link Options" that allow you do specify an .exe to run. The "Help Link Action" aligns to the "Help" Option when a user right clicks the WEM Agent Icon, however the "Custom Link Action" maps to the "Support" Option in the right click menu. Just to make things confusing. I couldn't get anything outside of a direct .exe to launch - couldn’t pass a switch or anything like that. Might be good for 3rd party support ticketing systems with a client front end etc

[![HelpDesk3]({{site.baseurl}}/assets/img/wem-client-side-tools/HelpDesk3.png)]({{site.baseurl}}/assets/img/wem-client-side-tools/HelpDesk3.png)

 WEM Agent Help and Support Options
{:.figcaption}

This is just a few of the tools for troubleshooting and environment management that I use day to day in WEM deployments, as well as a few bonus hidden capabilities.

I will start a series of posts shortly around the more advanced features for environment management, based on issues and challenges we are seeing across the Citrix Forums, as well as in day to day use of the solution.

Until next time....

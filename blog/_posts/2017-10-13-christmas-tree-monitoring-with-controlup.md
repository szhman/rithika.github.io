---
layout: post
title: "Christmas Tree Monitoring with ControlUp"
permalink: "/christmas-tree-monitoring-with-controlup/"
description: "My kids think ControlUp monitoring is like a christmas tree"
categories: [Citrix, ControlUp, Monitoring]
redirect_from: 
    - /2017/10/13/christmas-tree-monitoring-with-controlup
    - /2017/10/13/christmas-tree-monitoring-with-controlup/
image:
  path: /assets/img/christmas-tree-monitoring-with-controlup/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Christmas Tree Monitoring!

That's what I think when I hear the word [ControlUp](http://controlup.com/) nowadays… Probably due to my young children staring in awe at the ControlUp console in high definition on a large display in my office - "it looks like Christmas Daddy!"

The more I use this tool, the more I love it and would hate to be without it. Most of the time when I drop the name into conversation, most people simply think: "citrix monitoring"… which is true, but the tool is so much more and offers so many more features that make it more than just a performance monitoring platform

## Use Cases

Primarily I get the opportunity to drop ControlUp into an environment in two situations:

1.  A new Citrix deployment that I am involved with, and I want to monitor it from the word go. This helps me with sizing and a general feel for the environment. The fact it can plug into the Hypervisor platform and provide you end to end visibility, removes the guess work. Instantly.
2.  An existing environment with some sort of performance issues. I used to get a little nervous when jobs turned up with "performance" problems somewhere in the description, however now I am almost excited by them, because I have tools like this which shines the light very quickly on what is good, and what is not so good

## Agent and Agentless Monitoring - the best of both

What is really well thought out is the combination of [agent and agent-less monitoring](https://www.controlup.com/blog/agent-agentless/).

I can deploy this into an environment and point it at a vSphere platform (shown below), and it's completely non-invasive, yet provides me with all sorts of crazy metrics in a view that makes sense. It's not reinventing the wheel here, it's just displaying existing data in a way that is pleasant and easy to read. Combine it with predefined thresholds, and within minutes you can get a complete overview of where your issues may be starting, or, if you are lucky, that your environment is doing well.

[![ControlUpAgentVsAgentless]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/ControlUpAgentVsAgentless.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/ControlUpAgentVsAgentless.png)

Combine this with agent based monitoring for Windows Servers such as XenApp hosts and you open up a whole world of visibility. The amount of data that the little lightweight agent can retrieve is fascinating. All with ability to deploy on the fly, with no impact to the OS.

[![ControlUpAgent]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/ControlUpAgent.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/ControlUpAgent.png)

Agent and Agent-less as appropriate, integration with Citrix Solutions such as XenDesktop natively, and the ability to drill through your delivery groups is very cool

## Historical Data

[Control Up Insights](https://www.controlup.com/controlup-insights/) is a beast. Whilst Christmas Tree Monitoring is epic for real time analysis, the export, upload and roll-up of this data into Insights is exceptional for tracking, trending or root cause analysis.

How many times do you get a complaint that on Tuesday (it's now Friday) Citrix was slow and performance was awful. How do you currently address this? With Insights, you can simply go back and have a look at exactly what happened, at that point time. What did the servers look like, what processes were running, what was the user session looking like, what was the user doing? Was the user effected by someone else doing something funny?

One of the nice little perks provided in this pane, is the community bench marking statistics, where your results are anonymously compared to the greater results within the Insights ecosystem, and you are provided your statistics benchmarked against the community. Nice for end of year reports to your boss showing how well your environment operates, or the other way round to help you in your justifications come budget time

[![Insights1_machine]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Insights1_machine.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Insights1_machine.png)

## Application Performance

You may or may not have read up on [my post](http://jkindon.com/2017/09/25/enhancing-monitoring-and-visibility-with-free-control-up-tools/) around the free tools ControlUp provide to community where I take a quick look at the [Application Load time Profiler](https://www.controlup.com/blog/controlups-application-profiler-free-tool/). The full suite goes bananas as far as statistics go. We can track application load time performance, resource utilisation, launch occurrences and even learn who uses these processes and at what times.

[![AppMonitoring]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/AppMonitoring.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/AppMonitoring.png)

## Logon Performance

Given my focus on performance and optimisation work, user experience is of obvious importance to me. Understanding what my projects are delivering from an end user experience side of things is critical to everything I do. ControlUp offers me complete insight into every facet of the logon process, identifies in real time; bottlenecks, GPO performance issues, rogue logon scripts or just too much going on at start-up - I can see it in real time, and I can trend it over the duration of days, weeks, months, years. I can instantly see improvement with the work I do, and on the same token I can instantly see negative impacts due to changes in the environment.

[![LogonPerformance]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/LogonPerformance.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/LogonPerformance.png)

And extended into Insights for trending

[![Insights1_Logon]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Insights1_Logon.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Insights1_Logon.png)

## Session Auditing

An interesting use case that many don't think about for ControlUp is Auditing. I dealt with a client that was sold on ControlUp purely due to the visibility they had into user sessions and being able to trace exactly what people were (or weren't in this case) doing.

We can view processes launched, application run time, session idle or active times, and get a very clear picture into what a session looks like with numbers and figures to back it up. This leverages Insights and is extremely powerful.

From a real time perspective, you can take an actual snapshot, either notifying the user or not notifying the user and see exactly what is being done within their session. Powerful.

[![SessionAuditing]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/SessionAuditing.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/SessionAuditing.png)

## Additional Control Tasks

There is a myriad of cool, quick access admin tasks available to you from anywhere within the console. It is smart enough to be contextually aware, meaning that if I am focusing on server performance views, I am presented with quick actions relating to server functions I might want to perform such as group policy update, flush DNS, maintenance mode control, power management, remote to the server etc, however if I am focused on a user view, I am presented with options relating to user functions - log off, group policy update, refresh the user space etc.

[![ExtraTasks]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/ExtraTasks.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/ExtraTasks.png)

Checking out an existing running session changes what options are available within the action menu, or I can drop down and control processes relating to that user - very context aware across the board (Kill Policy is great for troubleshooting locked down sessions)

[![UserContext]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/UserContext.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/UserContext.png)

[![ProcessContext]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/ProcessContext.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/ProcessContext.png)

## Server Comparison

How many environments do you have to deal with where provisioning technology has not been implemented? No PVS, no MCS? Not great, but a reality we have to face.

The single biggest challenge with zero provisioning is the obvious risk of configuration skew. One change by one admin to fix one issue on one server for one user, instantly puts your XenApp Servers out of sync, and who knows what the flow on effect is?

ControlUp offers some cool functionality to allow you to compare your servers and view where configuration skew exists, it can compare registries, folders, applications installed, and quickly identify the differences between these servers. If you can't provision them, and you aren't enforcing some sort of standard build or automated resolution of configuration drift then you can at least see the differences.

Programs and feature comparisons:

[![Comparison_Applications]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Comparison_Applications.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Comparison_Applications.png)

File System comparison:

[![Comparison_Registry]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Comparison_Registry.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Comparison_Registry.png)

Registry Comparison:

[![Comparison_Services]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Comparison_Services.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Comparison_Services.png)

## Script Libraries

ControlUp gives a lot back to the community in their tools, and this extends into their commercial platform with a centralised [script library](https://www.controlup.com/script-library/) available for you to utilise cool or helpful scripts to analyse problems within your environment. These are community (and ControlUp) developed and offer you a vast selection of useful things for you to leverage - group policy logon times via PowerShell, IE processes running against a user, profile size etc. You can also find scripts to help you perform common actions against your servers or Citrix environment. Oh, and its contextually aware, so I only see user based actions for my user views, and computer for my computer views.

[![ScriptBasedActions]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/ScriptBasedActions.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/ScriptBasedActions.png)

## Centralised Event Logging

Who doesn't get frustrated at trawling through event logs of multiple servers? ControlUp console gives you a centralised view of all monitored servers. Like I need to write any more about the value in that.

[![EventLogging]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/EventLogging.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/EventLogging.png)

And again, extended into Insights:

[![Insights1_Events]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Insights1_Events.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/Insights1_Events.png)

## Remote Desktop Manager

This is a neat little tool provided as part of the Console. Why not *Shrugs*

[![RDP]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/RDP.png)]({{site.baseurl}}/assets/img/christmas-tree-monitoring-with-controlup/RDP.png)

## Summary

I could go on and on about the value of this tool, however this is more aimed to show you on how its more than just a performance monitoring solution - check it out, you can get a free trial and instantly gain insight. If you are even just a little curious about what your environment is doing, put it in and turn it on, you might well be surprised at what you see.
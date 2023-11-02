---
layout: post
title: "Citrix Session Management Policies"
permalink: "/citrix-session-management-policies/"
description: Delving into the options for Session Management within CVAD environments
categories: [Citrix, CVAD, Azure, Session Management, Windows]
redirect_from: 
    - /2021/08/09/citrix-session-management-policies
    - /2021/08/09/citrix-session-management-policies/
image:
  path: /assets/img/citrix-session-management-policies/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Session management and timeout policies in Citrix platforms are often a point of confusion and head scratching. This has extended out slightly to Azure Virtual Desktop with the introduction of Windows 10 multi-session. This is a quick post around the options and configuration points for the different OS types.

As a rule of thumb, when dealing with a standard Citrix deployment, there are two operating systems types when we think about Windows.

-  Single Session OS
-  Multi Session OS

Typically, a Desktop OS (Windows 10) is known as a single session OS, and a server platform such as Windows Server 2019 would be known as a multi-session OS. Windows 10 multi-session speaks for itself; an Azure only Windows 10 offering that allows multiple users to log into a single Windows 10 machine.

When talking session management and session timeouts. Windows 10 Multi-Session plays by the same rules as a Windows Server Operating System.

There are 3 common session timeout policies that we think about

-  Session connection timer – regardless of the session state, how long can a session be alive (this not often used)
-  Session Idle timer – how long can the session be idle before we disconnect it
-  Disconnected Session timer – how long can a session be disconnected before we end it

Below we will walk through the configuration points for each.

## Citrix Policies – Single Session OS

In the Citrix world, Citrix Policies driven from either Citrix Studio, or nested into a Microsoft Group Policy object, allow us to drive single session sessions timeouts. These policies do not apply to Multi-Session Operating Systems including Windows 10 Multi-Session.

[![Citrix Policy Settings for Single Session OS - Idle]({{site.baseurl}}/assets/img/citrix-session-management-policies/CtxPol_SessionIdle.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/citrix-session-management-policies/CtxPol_SessionIdle.png)

[![Citrix Policy Settings for Single Session OS - Idle Value]({{site.baseurl}}/assets/img/citrix-session-management-policies/CtxPol_SessionIdleValue.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/citrix-session-management-policies/CtxPol_SessionIdleValue.png)

Single-Session OS session timer policies via Citrix Policy are user-based policies, meaning that you can target different timer policies to different user groups.

## Group Policy or Registry Keys – Multi-Session OS

In the same Citrix world, and to be fair, any delivery platform that utilises Multi-Session OS, Group Policy Objects are available to control session limits. These policies apply to both Server Operating Systems and Windows 10 Multi-Session and are delivered typically at the machine level and as such, may require segmentation of machines via different OU’s or policy filtering. The same policies exist in the user context, however loopback replace processing should always be used, and as such, per user settings in the user context via GPO are not really something I am going into. AppMasking may be an interesting option here to be able to create limits on a per user basis – I have not tested this at the time of writing but will update when I get a chance.

[![GPO Policies for Multi Session OS]({{site.baseurl}}/assets/img/citrix-session-management-policies/GPO.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/citrix-session-management-policies/GPO.png)

The timeout policies offer a selection or pre-defined values via GPO, however, can be more fine tuned by writing the specific registry key values directly (Thanks James Rankin). The values are written under the following Key: HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\

[Admx.help has all the detail you could want](https://admx.help/?Category=Windows_10_2016&Policy=Microsoft.Policies.TerminalServer::TS_Session_End_On_Limit_2)

## Citrix Cloud – Autoscale Dynamic Session Timeouts – Multi-Session OS

Citrix Cloud offers a more advanced way of dynamically controlling session timeout policies for multi-session Operating Systems via the [Autoscale feature](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/manage-deployment/autoscale/dynamic-session-timeout.html). There are two key timers that can be set here based on either a **peak** of **off-peak** schedule. This allows you to be more aggressive in an off-peak scenario with a view to allow Autoscale to be more aggressively scale down workloads.

Firstly, you must set accurate peak and off-peak windows in your Autoscale configurations

[![Autoscale Schedule Configurations]({{site.baseurl}}/assets/img/citrix-session-management-policies/Autoscale_Schedule.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/citrix-session-management-policies/Autoscale_Schedule.png)

You may well have differing peak times for Weekdays vs Weekends. Make sure these are accurate.
{:.think_about_it}

Then you configure advanced timeouts under the Dynamic Session Timeouts node.

[![Autoscale – Dynamic Session Timeouts]({{site.baseurl}}/assets/img/citrix-session-management-policies/Autoscale_Dynamic.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/citrix-session-management-policies/Autoscale_Dynamic.png)

The above example results in the following:

-  During my peak times window of 08:00:00 through to 18:00:00 (8am to 6pm), my idle session timeout is 60 minutes, and my disconnected timeout is 90 mins
-  Between the hours of 18:00:00 back through to 08:00:00 (6pm to 8am), idle timers are set at 30 mins and disconnected timeout is 60 mins

## AD User Account Properties

Active Directory allows configuration of session timeouts on the user account object itself under the sessions tab. These settings are not something I see used often and are overridden by any other value written. These settings are effectively a catchall and apply to any logon for that user that does not have an overriding value.

[![Active Directory User Account Properties]({{site.baseurl}}/assets/img/citrix-session-management-policies/ADProps.png)](https://github.com/JamesKindon/jkindon.github.io/blob/main{{site.baseurl}}/assets/img/citrix-session-management-policies/ADProps.png)

## Summary

To break it down:

-  Single Session OS timeouts are addressed via Citrix Policies
-  Multi-Session OS timeouts are addressed via GPO or Registry Keys (including Win10 Multi-Session)
-  Citrix Cloud Customers have advanced capability via the Autoscale feature for Multi-Session OS workloads which replace the above GPO or registry settings
-  ADUC settings are available and impact all sessions however are easily overridden

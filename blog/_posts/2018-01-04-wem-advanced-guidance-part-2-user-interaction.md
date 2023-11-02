---
layout: post
title: WEM Advanced Guidance Part 2 - User Interaction
permalink: "/wem-advanced-guidance-part-2-user-interaction/"
description: WEM Advanced Guidance Part 2 - User Interaction (Historical Post)
categories: [Citrix, WEM, Windows, CVAD]
image:
  path: /assets/img/wem-advanced-guidance-part-2-user-interaction/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
related_posts:
  - blog/_posts/2017-11-30-wem-advanced-guidance-part-1.md
  - blog/_posts/2018-02-08-wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly.md
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

This is an [old post](https://blogs.mycugc.org/2018/01/04/wem-advanced-guidance-part-2-user-interaction/) which I initially wrote with Hal Lange for CUGC, however the indexing on their site isn't great, so I have ported the content here, and scrapped out any legacy guidance. For up to date recaps 5 years on, see [WEM Advanced Guidance 2023](https://jkindon.com/wem-advanced-guidance-2023)
{:.warning}

The [first part of](https://jkindon.com/wem-advanced-guidance-part-1) this series looked at the some architectural and thought processes around Workspace Environment Deployment into your environments. We focused primarily on the infrastructure and configuration options from an administrative perspective. In this second part, we are going to look at how WEM interacts with the users it is working to look after, and what your options are around how the users and WEM engage with each other, as well as some guidelines around how to optimize or make this engagement smooth and relatively seamless.

Before we dig into some of the settings, I would like to clarify that throughout this article, when we say, "By Default," we mean a blank Configuration Set with nothing imported. If you import the recommended default settings, many of the options we are discussing below are preconfigured in a decent fashion, however you still need to customise to your environment and your user requirements.

## VISUALS

What does the user see? The first thing you really think about when talking about putting environment control is, "What is the user going to see, and are they going to complain about?" There are two types of users in this situation:

-  The annoyed, paranoid user. This one sees something new, flips out, and your new solution is now the reason they haven't been able to work. We all have them.
-  The second type is the curious user, who sees something new and clicks all over it trying to end its existence – typically breaking it and turning into the user above.
Our goal is to protect you from both, so it's good to get an understanding of what the user will see. At a high level, they will with default settings, see the following:

-  The Agent Splash Screen

[![WEM Splash Screen]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem.png)

-  The Agent Icon in the taskbar with a set of default right-click context options

[![WEM Agent Icon]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-taskbar.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-taskbar.png)

-  **Administrative refresh feedback**: when a session refresh is forced by an administrator, the user will see a pop-up within their session (tooltip)

These settings are configurable, and these settings really should be configured at the start.

There are multiple areas where you can configure these behaviours:

**Advanced Settings -> Configuration -> Main Configuration: Agent Service actions**

[![WEM Agent Service Actions]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-agent-service-actions.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-agent-service-actions.png)

Most of the above are self-explanatory, you can control the Agent Launch behaviour, and the Agent Type.

There is an option to launch only the cmd agent in published applications, however it's important to note that if you enable this setting, there is no way to hide the CMD window. If you are wanting to hide any sort of interaction with the user in published applications, but not so much in published desktops, then you are better to use the "Hide Agent Splashscreen in published applications" setting found in the below section.

Virtual Desktop compatibility tells WEM to launch the agent when the user is logged in to session 1. This basically says, "turn on" for Windows Desktops, physical or virtual (VDI)

**Advanced Settings -> UI Agent Options**

[![WEM Agent UI Options]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-ui-agent-options.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-ui-agent-options.png)

These options allow you to select some different themes for your agent interaction for the users. You can also control agent splash screen interaction (Hide Agent Splashscreen), as well as change what options are available to the user if they right-click the WEM Agent icon.

One setting that I really like is the **"Disable Administrative feedback"** setting. This basically tells WEM not to interact with the users with any sort of notification around successful or failed refresh actions which are triggered by the Admin

## ACTIONS, CONNECTIONS, AND REFRESH OPTIONS

WEM has multiple components it uses to build the user environment:

-  Profile Management and User State Virtualisation (Folder Redirection)
-  Environmental Policies to provide some basic feature lockdowns that we may have traditionally actioned via Group Policy ADMX settings
-  Actions and Assignments. These make up almost everything we would have applied with slow processing Group Policy Client Side Extensions (or Group Policy Preferences)

Building these things out is pretty simple. For Profile management you tick a few boxes, add a few settings and away you go. For USV, you define your environmental pathing's and you are done. For actions, you define what you want to do, you create some conditions and rules so you can selectively apply them, and you assign them to your users and groups. Done.

There are however some things to think about when building out these environments, particularly around the action assignments, and how these should look and feel for the end user, both on first-time logon, additional logons, and with mid-session refresh.

**Advanced Settings -> Configuration -> Main Configuration -> Agent Actions**

[![WEM Agent Actions]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-agent-configuration.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-agent-configuration.png)

Basically, what actions WEM will try and process on initial session establishment.

**Advanced Settings -> Configuration -> Cleanup Actions**

[![WEM Agent Cleanup]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-agent-cleanup.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-agent-cleanup.png)

These settings basically control how the environment will behave for "cleaning" or "preparing" the environment at initial session establishment. Enabling the delete options will delete everything defined, regardless of these being defined by WEM. If that content is static, there really isn't much point in deleting these things at start-up, however if you have a dynamic environment where you use a lot of different filters and conditions, then it may make sense to delete and let WEM recreate again.

**Advanced Settings -> Configuration -> Advanced Options**

[![WEM Advanced Options]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-advanced-options.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-advanced-options.png)

The **agent actions enforce execution** settings control how refresh behaviour is controlled for actions. For example, without these options enabled, WEM will process the action and record the result on logon. The next refresh of the agent won't force the actions to be re-applied if no changes have been made. You can override this behaviour here so that WEM applies all actions at every refresh regardless of changes being made or not. Obviously doing so can have a direct impact on the user experience and as such you should be cautious around which options you enable.

The ability to **revert actions** simply means to reverse them. If you have something that you have unassigned on the fly, at the next refresh interval, the initial action result will be reversed. A simple example – if you created a mapped drive action, and a user receives the drive at 9am when they log on. If you change the assignment at 9:30am, and remove the drive assignment at the next refresh – say 9:45am, the drive will be removed from the user.

**Advanced Settings -> Configuration -> Reconnection Actions**

[![WEM Reconnect Options]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-reconnect.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-reconnect.png)

You can control the reconnection processing options here. If you would like all actions to be re-processed at every reconnect, then enable these options.

These settings can have a not-so-great effect on the environment and can result in strange behaviours such as what a user will perceive as flashing or shifting/moving of items. This is particularly of note when applying registry keys and FTA assignments and when redirection is in place. The reason for this behaviour is due to the "Notify_ShellChange" command issued by WEM against the environment when processing these two particular items. Darrin DiNapoli did some nice testing on this on a citrix discussion post found here.

**Advanced Settings -> Configuration -> Advanced Processing**

[![WEM Advanced Processing]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-advanced-processing.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-advanced-processing.png)

By default, when you assign filters to an action, the filters are only looked at and processed on the first logon. If you reconnect a disconnected session and have reconnection actions processing enabled, WEM will apply what it applied to set the state of the first session.

If you enable **Filters Processing Enforcement**, WEM will process filters on every session reconnect. This can be handy if you have associated an **"User SBC Resource Type"**" Condition, and you have switched between a published desktop and a published application within the same configuration set, if filter processing is not enabled, the condition will be ignored, or if you have filters that change for say, time of day...

## ENVIRONMENTAL SETTINGS

The environmental settings component of WEM is where we can take over control of things that we traditionally drive via Group Policy Settings. The basics are self-explanatory, tick the box and get the result.

[![WEM Environmental Settings]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-environmental.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-environmental.png)

There are some additional environmental settings of note that can be found under the **Advanced Settings -> Configuration -> Agent Options** node. These you should be careful with.

[![WEM Refresh Settings]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-refresh.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-refresh.png)

Depending on the OS you are working with, the option to refresh appearance can result in issues with legacy applications, such as flashing or blinking screens, warped graphics in multi-windowed published apps etc. Additionally, refresh desktop combined with some of the settings discussed above can result in quite an interesting sensation for users as their taskbars, desktop icons, even start menus may appear to have a random convulsion whilst the environment is refreshed.

Under the extra features node, there are a few things of note:

1.  **Initial environment clean-up**. This option I am very wary of, particularly if you are implementing WEM into an existing environment. This option basically looks at the differences with what you have told WEM to build, and what exists, and deletes the differences. Now you can see straight off the bat here the problems for users that have their environments already in existence. I have also seen it destroy the WinX shortcuts for the start menu in windows server 2016 (took me a while to track it down).
2.  **Initial Desktop clean-up**. See above, however, this is kind of a less aggressive tact and just cleans the desktop rather than everything.
3.  **Check Application Existence**. This is very handy if you are building out start menus, or plugging in shortcuts to the desktop, etc. with WEM. This setting will check to see if the shortcut target actually exists before creating the shortcut – great if you are managing multiple images – would suck for users to be clicking on icons that go nowhere.

## ACTION SELF-HEALING

Action self-healing is an interesting concept. If you enable self-healing, you are basically telling WEM to override anything a user does to the environment that you have built for them in relation to actions.

[![WEM Self Healing]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-self-healing.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-self-healing.png)

Say I publish a shortcut to the desktop, with no self-healing enabled. The first time WEM processes this setting for the user, it will dump the shortcut where I tell it to. If my user doesn't like the shortcut and deletes it, WEM doesn't care. However, if I enable application self-healing, if the user deletes the shortcut, WEM sure as hell does care, and puts it back. Over and over and over.

You can start to see the control that you can gain here, as well as the flexibility you have in doing a first-time setup style configuration and then leaving the door open for users to customise – or both. What you should also be able to see here, is how important understanding the refresh and processing actions we discussed above, that they all play into each other and being over aggressive with refresh settings and self-healing options can lead to cranky users – and WEM is all about making users happy (or in our world, not yelling).

## PRINTING

Printing within WEM comes with some interesting behaviours that should be noted, as printing in all environments is a temperamental area that sends users into a whinging frenzy.

WEM has multiple facets to printing:

-  Importing printers from an existing print server and creating them as assignable actions
-  When assigning these printers to users, default printers can be set and the usual conditions can be applied
-  Initial Printer processing can include the deleting of printers on each logon
-  Specific Printers can be preserved if we don't want them deleted
-  Session Printers created by Citrix Policies can be exempt from WEM printer processing

**Advanced Settings -> Cleanup Actions: Printers deletion at startup**

[![WEM Printing]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-printing.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-part-2-user-interaction/wem-printing.png)

Citrix Policies are something that should well be considered when dealing with WEM printers, particularly around redirection, session printing and defaults. Conflicting settings can cause awkward results with WEM, particularly as WEM processes quickly, and may clash with Citrix Policies.

The other interesting behaviour of note is the way that default printing is handled and how this interacts with users.

For example, in a perfect world, WEM creates your printers for your users, you assign the default printer for them and off you go. But what if this isn't the case, and you have users that need to set their own default printers and manage them accordingly. In this scenario, there are some points to note:

-  The WEM Print Management Utility should be used by users to set the default printer. This is because WEM records the default value set by that utility and relies on this for future processing.
-  If WEM assigns a printer to a user, default printers **set by the user** in the Windows Control Panel are ignored the next time WEM processes the printer.
-  WEM will revert to the last known default that was set by the WEM Printer Utility (if you are not assigning a default printer via a WEM Assignment) or it will set a random one based on its processing options and assignments if a default hasn't yet been set.
-  The control panel printing utility is ignored and should be removed from user access if using WEM to assign printers without assigning a default.
-  Citrix print policies that redirect and retain default printers can be broken if you don't enable the appropriate processing settings for logon processing.
This behaviour is the same regardless of processing enforcement options that are set within WEM (remember these just tell WEM how to reprocess and what to reprocess – it doesn't change the actual behaviours of the action assignments).

Like with anything EUC-based, and consistent with what we have suggested previously, choose an assignment process for Printers and stick with it. Mixing assignment tools will only result in frustration and unexpected results. Choosing to use the WEM printer utility requires next to no configuration, is very easy for users to drive, and provides a much more expected set of behaviours.

## SUMMARY

This is the second joint post Hal Lange and I have completed in this series, and we hope it's been of use. The more WEM is getting out into environments, the more learning we are gaining. The Citrix Forums are a great place to get input and assistance around WEM and related tools, and there is some great community input coming into those discussions which are helping to grow the knowledge set out there. Drop by and start a post if you have any issues or items that you would like addressed.

Until next time...

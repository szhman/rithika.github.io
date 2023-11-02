---
layout: post
title: "The User Environment Management Puzzle"
permalink: "/the-user-environment-management-puzzle/"
description: Demystifying the what and when of user environment management
categories: [Apps and Desktops, Citrix, Profiles, Start Menu, UPM, WEM, Windows, XenApp, Optimization]
redirect_from: 
    - /2020/01/20/the-user-environment-management-puzzle
    - /2020/01/20/the-user-environment-management-puzzle/
image:
  path: /assets/img/the-user-environment-management-puzzle/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

I have worked in the EUC world for a while now with a focus on user-experience. Admittedly the vast majority of my skillset sits in the Citrix based delivery of these workloads, however, I often stumble across engagements where native RDS services or alternative solutions are in place, and I find that the concepts I run within my projects are principals that I can apply for all.

I have put this post together to help outline the methodology and logic I tend to use across my projects so that admins and consultants alike can understand what and why I have put in what I have. Often there are so many moving parts to an environment, that it can be a touch overwhelming to admin teams who take on what is left in place, and it's very easy for them to revert back to old habits or diverge from the practices we follow during implementation once the project is into a BAU state.

Hopefully, the below methodology helps provide clarity into my view into what goes where and when it goes there. This is my view on the world and does not in any way dismiss other opinions or options available. I have broken the post into three key pillars:

-  Image Configuration and Preparation – the configuration of an image with a focus on user experience and management
-  Image Optimization – ensuring that an image is running lean and neat without useless junk getting in the way of a positive user experience
-  User Environment Management – what tools go where and why I use them where I do. Includes some key concepts and important aspects which can have a large impact on the user experience

## Pillar 1: Image Configuration and Preparation

### Group Policy – Machine Based

Group Policy offers not just an execution engine for both computer and user-based settings, but a way of when used correctly, offering a form of living breathing documentation.

Computer-Based Policies and Preferences offer a way to address 99% of environmental alterations you make at a machine level. What it can't do natively, it can execute by means of a Script so there really are no limits in what you can do in this context.

A base image should be nothing other than Windows with the appropriate applications installed. Configuration of the environment is peripheral.

An image ideally should not contain static or one-off configuration items where someone who installed a finance application also copied a font file to the system, ran a manual registration of a particular DLL for an add-on service and then changed a setting in an ini file to make it all work. All of these actions should live in a Group Policy Object in a machine context allowing for anyone to perform the basics, and the Policies to handle the rest.

Some will not like this methodology, but if a GPO doesn't impact the user experience, I am sold, and if it means that a simple rebuild of an image can be undertaken over and over, with the environment dynamically keeping up, then it's a win in my books.

Some common things I like to store centrally and address with Group Policy Preferences in a machine context:

-  Ini file management
-  File and folder management (store any configuration files centrally and copy over on machine start-up)
-  Start Menu Layouts (Keep your Layouts Central and copy them locally)
-  FSLogix AppMasking files (or BIS-F)
-  Start Menu common shortcut creation

Anything machine-based that builds the environment in a dynamic fashion or controls the environment in a positive user experience impacting way.
{:.decision}

### Base Image Script Framework

The [Base Image Script Framework (BIS-F)](https://eucweb.com/download-bis-f) is primarily a sealing solution. Image preparation is arguably one of the most mismanaged components of an environment. BIS-F is often referred to in the Optimization context of a discussion, and whilst this is true and it does a small level of optimization, its primary role is to prepare your images for provisioning. It is a sealing solution broken into two phases

1.  Preparation phase – this phase is about preparing your image for the fact its about to be used for x number of machines which are going to need to appear unique
2.  Personalisation phase – this phase occurs on start-up and handles the fact that the machine has been previously prepared, and now needs to be personalised

It is an aggregator and a scheduler of many facets of an environment that require consideration in a non-persistent scenario, it handles the preparation of not just Windows, but antivirus, update services, Citrix & third party services, as well as other key components such as FSLogix.

BIS-F has a level of optimization it performs, but also hands off these services to the executor of your choice, for my environments this is always Citrix Optimizer.

BIS-F is also highly customisable so should there be unique requirements to the environment that requires custom scripting to cater for, then you can simply ask BIS-F to run custom scripts as part of the sealing process.

After any image build for any EUC facing workloads, BIS-F is a no brainer.
{:.decision}

### Default File Type Associations

A [default file type associations file](https://docs.microsoft.com/en-us/archive/blogs/windowsinternals/windows-10-how-to-configure-file-associations-for-it-pros) is not always required for all environments, however, for those that want to make some basic changes to the defaults across the board for all users, the default file type associations path is the most stable one.

It is a simple XML file that is supported by a computer-based Group Policy deployment model. Set and forget for the most part until you need to make changes.

Browser management, PDF handlers, document handlers etc are the common trigger points to go and deploy a default file type association file in an environment.

There may also be user-based FTA requirements. I touch on these later on.

If there are machine-wide defaults that need to be set, a default file type associations files is the way to go.
{:.decision}

### File Handlers

File handlers work hand in hand with default file type associations. In modern Operating Systems, there are some sub-optimal handler configurations by default. For example, in Windows Server 2016, Microsoft Paint is the default File handler for graphics files, and for Windows 10, these same files are handled by a Modern App named Photos.

Luckily there are handlers such as the old Windows Photo Viewer which is just waiting to be turned on and happiness granted back to the users.

Per Project basis for the most part, however low hanging fruit like the example mentioned above is pretty much standard. Most modern apps have a Win32 alternative that can be leveraged
{:.decision}

### Sysinternals Autoruns

Sysinternals. What would any of us do without the amazing toolkit that has underpinned the solution to half our problems in the last 20 years?

Autoruns is a critical tool that shows you exactly what happens when the machine starts-up and the user logs in from a process execution perspective. It will show you what runs, in what context it runs, and allow you to dynamically kill those that don't need to run.

I use Autoruns at the end of every image update prior to sealing with BIS-F. This allows me to address any lovely nuggets that new applications may drop into the environment which will negatively, and uselessly affect the user experience. If there are items that I need to have auto-started from a user standpoint, I will typically have Citrix Workspace Environment Management drive the execution on a requirements basis.

Every environment after every image update to sanity check no unforeseen impact.
{:.decision}

### FSLogix AppMasking

FSLogix App Masking is the best option for effectively and efficiently hiding applications from users that should not have access to them.

Many organisations traditionally leveraged Microsoft AppLocker to handle the restriction of access to specific applications, however, whilst it did the job, the user experience was not a nice one in comparison to AppMasking. With AppLocker, the executable was visible and would throw an unauthorised error should the user not be allowed to run the application, with AppMasking, the user never sees the application start with. If you can't see it, you can't run it. Perfect from a user experience standpoint.

AppMasking has a few other tricks - a post maybe for another time, but I feel it's worth mentioning that it's more than just hiding applications from users. It can be used for all sorts of file system trickery such as redirects in place of traditional SymLinks, Application Containers etc.

Any environment that requires customisation of what users should have visibility towards, including as a default, Windows Start Menu (read on)
{:.decision}

### Microsoft AppLocker

[Microsoft AppLocker](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) still holds a very important role in a EUC environment, I stare at it with a security face on and leverage it for any form of restrictions that have a security focus.

Whitelisting and Blacklisting of certain areas on the file system to address malware execution etc are best handled by Microsoft AppLocker, hiding of applications for compliance or basic user experience requirements is the job of FSLogix AppMasking.

Any security-based whitelist/blacklist requirement. Driven by either GPO or Citrix WEM.
{:.decision}

## Pillar 2: Image Optimization

### Citrix Optimizer

Windows is fat and heavy. It is filled with so much useless garbage that my brain hurts trying to keep up with it all, a very large portion of it completely irrelevant and actually counter-productive for a controlled VDI environment. We are all big enough to know the rubbish I am talking about here so I won't go into details, but needless to say, a lot can go.

If you are operating a Citrix Environment, then running the Citrix Optimizer is a no brainer. It is the single best gentle way of optimizing an image in a supported fashion. I use this everywhere and without hesitation across all supported platforms.

Delving into the community-based optimization templates is also a really cool way if you want to see what others are doing in their environments. As a consultant, I want gentle and guaranteed results so I tend to stick with the basics, but there is always more than can be done should you have time to play.

Always. I see next to no reason to not utilise this tool.
{:.decision}

### Modern Apps

I am going to take a special moment to address the bucket of useless rubbish that is Modern App management in Windows.

The impact of modern apps to user experience in a non-persistent (and even a persistent) VDI environment should not be underestimated, in fact, industry research including that of the Citrix Optimization team categorically prove that the removal of Modern Apps from your images has the single biggest impact on the environment.

I mentioned above that Citrix Optimizer is the gentlest way of dealing with optimizing an environment, it will handle a large amount of Modern App removal with a guarantee of not breaking things. There are still additional Modern Apps that can be happily stepped on should you have alternate solutions for your users in the form a win32 application replacement (think calculator) and there are [many scripts out there which will assist you in removing that scum from your images](https://github.com/SCConfigMgr/ConfigMgr/blob/master/Operating%20System%20Deployment/Invoke-RemoveBuiltinApps.ps1).

If they aren't required by the business. Remove them.
{:.decision}

## Pillar 3: User Environment Management

### Group Policy – User Based

I have a large focus on the Citrix Workspace Environment Management solution, and it's my preferred management tool in every project I execute. This does not mean however that I am not a Group Policy fan as well, in fact, it's the opposite. If an ADMX template is available to handle the control of something I want to be controlled, I leverage it (Microsoft Office, 3<sup>rd</sup> party utilities, security, application configurations etc). There is nothing better than GPO for the living and breathing documentation of an environment. The more you can put it into GPO the better as long as there is no negative impact on the user environment.

And as always, I am not talking about Group Policy Preferences in this context.

Group Policy Preferences in the user context offer a huge amount of flexibility and capability, typically at the tax of the user login experience. They are great when they structured properly, but a nightmare on the user experience when things go wrong. Not all preferences are avoidable, some in the user context offers a more simple way of delivering settings than other alternatives (think regional settings for example).

If there is an ADMX template that provides configuration settings in the user context then I'll leverage it. Group Policy Preferences in the user context are typically second to WEM being the preferred delivery agent where possible.
{:.decision}

### Workspace Environment Management

Workspace Environment Management (WEM) is the tool I choose to use across my engagements to handle the user portion of the environment. Anything to do with items such as Drive Maps, Printers (time and place), Environment Variables, Folder Redirection, Registry items, Ini File management and Application presentation is handled by WEM.

I do not try and port too much into WEM that can be more easily and natively handled by an ADMX based policy. The tax of Group Policy is not a large one when dealing with clean Policy settings and the benefits of having a living and breathing, easy to consume documentation system in the form of Policy is worth the processing time (negligible).

Group Policy User-based Preferences, however, are a completely different story.

WEM can do much of what Group Policy Preferences can do in the user context, and also has a great get out of jail free card in the form of external tasks when required to execute not native WEM actions such as scripts or command-line executables.

If I see a logon script, it moves to WEM, if I see something show up in Sysinternals autoruns that looks ugly, I will validate the requirement and then move it to WEM to action instead. In fact, anything user-focused that impacts the logon times will typically move to WEM. [I've blogged enough over the years about WEM](https://jkindon.com/?s=wem), so will leave it there for now.

If there is an entitlement for WEM, it should be there for system optimization. How much moves into WEM is a per customer conversation, typically anything GPP based in the user context is an ideal candidate for a move
{:.decision}'

### Profile Management

Profile management is the component of the environment which handles, well, the user profile. In my world, it comes in two flavours. Citrix Profile Management or FSLogix Profile Container.

I don't like to have anything in a profile that cannot be replaced. Keeping a copy of critical data extracted and housed elsewhere is quite important. Tools such as Microsoft UEV and basic environment controls such as logoff scripts or scheduled tasks to move critical components of a profile elsewhere can give you flexibility and continuity in the scenarios where the same profile may not always be available (heaven forbid!)

Regardless of the technology, a great practice for learning what hurts is to delete a profile and see what manual configurations are required to restore the user to a happy place. Then automate it. I have written previously about [my thoughts on profile management](https://jkindon.com/2019/06/12/profile-management-in-2019-what-how-why/) which outlines the differences and where the technologies fit.

Most (not all) environments will require a level of profile management. Optimizing it is paramount in file-based solutions, managing size typically more important in Container-based technologies.
{:.decision}

### Start Menu Management

Don't underestimate the power of presenting a clean stable and consistent Start Menu to a user base. I typically now manage and configure the delivery of the Start Menu via FSLogix AppMasking.

You can read both [my article](https://jkindon.com/2019/10/09/appmasking-the-windows-start-menu/) and James Rankin also wrote a [cracking post](https://james-rankin.com/videos/dynamic-start-menu-on-server-2016-2019-and-windows-10/) on the topic.

The Windows Start Menu is pretty much the users first touchpoint into their environment. Ensuring its managed, and flexible and robust is critical. A job often much easier said than done.
{:.decision}

### Control Panel and the Immersive Control Panel

Admins have been locking down Control Panel Access for years, the immersive control panel (the **_settings_** button) came quite late to the party when it comes to administratively locking it down, however, it can easily be achieved by either Group Policy or [selectively via WEM](https://jkindon.com/2018/09/29/selective-control-of-the-immersive-control-panel-settings-in-server-2016/) using reg keys natively on a whitelist/blacklist methodology.

The key is to find a nice middle ground of control vs enablement to allow the basics for users without presenting rubbish they don't need. I typically start with a selection as below:

`Showonly:notifications;multitasking;printers;mousetouchpad;personalization-background;colors;lockscreen;themes;personalization-start;taskbar;dateandtime;regionlanguage;defaultapps;display`

Per customer for the most part and always good to align the lockdowns of both Control Panels to avoid confusion.
{:.decision}

### User-Based Default File Type Associations

In addition to machine-based FTA, there are times where you will need to adjust the FTA on a per user basis. This typically comes in the form of PDF handling requirements, or even default browsers etc

This is not natively possible in Modern Windows OS, I have [written previously](https://jkindon.com/2018/01/02/file-type-association-with-wem-and-setuserfta/) on the tools I leverage to handle this requirement.

I am including a note here to capture that when combining FSLogix AppMasking with user-based FTA requirements, things may often get hairy. The first point of troubleshooting is to remove any ruleset which may involve the File Type Handling you are trying to customise.

Per customer requirements.
{:.decision}

## Summary

As always, the methodologies contained above are based on my own experience and views. None of them are exactly mind-blowing or game-changing, in fact most of us probably follow a similar pattern, however, they are concepts and rules that I find work well given the space I focus in. There are many other aspects to an environment, with most being unique to a business and its user base.

No one-size-fits-all and your mileage will, of course, vary.

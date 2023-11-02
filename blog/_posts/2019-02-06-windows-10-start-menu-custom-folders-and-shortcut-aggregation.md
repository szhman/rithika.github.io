---
layout: post
title: "Windows 10 Start Menu Custom Folders and Shortcut Aggregation"
permalink: "/windows-10-start-menu-custom-folders-and-shortcut-aggregation/"
description: "Diving into shortcut aggregation and indexing in the Modern Start Menu"
categories: [Citrix, Apps and Desktops, Start Menu, Windows]
redirect_from: 
    - /2019/02/06/windows-10-start-menu-custom-folders-and-shortcut-aggregation
    - /2019/02/06/windows-10-start-menu-custom-folders-and-shortcut-aggregation/
image:
  path: /assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

### Intro

The Windows 10 Start Menu is like the gift that keeps giving. Every corner is odd behaviour or barely documented fun which can lead to a high level of "WTF" moments. The latest one for me is all around the creation of custom folders within the Windows 10 or Windows Server 2016 Start Menu, a simple request, that has next to nothing documented (that I could find) around the behaviours and expected outcomes. Big shout out to [James Rankin](https://twitter.com/james____rankin) who was my first point of call for any weird Windows 10 junk that I couldn't wrap my head around. We beat the same wall on this with two different heads.

Here was the simple request spawning from a Citrix Discussions [post](https://discussions.citrix.com/topic/400928-is-it-possible-to-add-custom-foldersfiles-to-the-server-2016-start-menu). "Add a custom folder to the Windows 10 Start Menu". Easy you say (easy I said)….well it is, as long as you understand the limitations and behaviours around how the Start Menu behaves Vs what happens in the file system. This may be well known, and I am simply thick, but here goes the outline.

## A quick recap on the Start Menu

The left-hand side of the Start Menu is made up of two main sources of "shortcuts"

-  The Users profile location `%AppData%\Microsoft\Windows\Start Menu\Programs`. This is pulled from the Default User Profile on the server where the profile is created. Exception to this being if you define a Mandatory Profile for your users
-  The Common users Start Menu Directory `%ProgramData%\Microsoft\Windows\Start Menu\Programs` This location is also the area where "Tiles" (The right hand side) are pulled from when defining a custom Start Menu Layout. See my posts [here](https://www.citrix.com/blogs/2018/05/30/customizing-the-windows-10-start-menu-part-ii-what-to-do-post-deployment/) around how this works

The Start Menu will combine `all` results from `both` of these locations and display a single folder depth (1 folder deep) aggregation of all shortcuts in all folder structures below these directories. I wrote about this a little more previously [here.](https://jkindon.com/2019/01/29/modern-start-menu-management-and-windows-toolbars/) The below image outlines this process in real time, with a folder named `James` in both my user profile, and then Common Start Menu location. Windows will show a nice cleaned combined view. Easy.

[![Nicely Aggregated Applications]({{site.baseurl}}/assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/HappyAggregation.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/HappyAggregation.png)

Nicely Aggregated Applications
{:.figcaption}

## The Lesser Known Nasties

As with all things Windows 10, there is a surprise in store. There are rules that apply.

### Rule 1: Indexing

There is some sort of index process that aggregates these sources together. As such, there is a rule that mandates the Start Menu will only display `one (1)` instance of each shortcut based on the executable path.

What this means in English is that while you can specify `c:\windows\system32\calc.exe` multiple times, the Start Menu will only ever honour 1 instance of it. You cannot have two copies of that same executable path defined anywhere in the Start Menu and expect it to be displayed.

It appears that a custom created Start Menu Folder housed in the user profile `%AppData%\Microsoft\Windows\Start Menu\Programs` will be the most authorative, followed by default folders such as _Windows Accessories_ within the same profile, and then finally the common start menu location `%ProgramData%\Microsoft\Windows\Start Menu\Programs` though this is just from my testing.

The images below display the behaviour of the `calculator` shortcut when it is defined in both the Common Start Menu `(%ProgramData%)` and the User Profile based Start menu `(%AppData%)`

> Note that the Profile based Start Menu `(%AppData%)` overrides and owns the shortcut in the `James` Folder.

[![Profile based Start Menu taking Precedence]({{site.baseurl}}/assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/CalculatorAggregation.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/CalculatorAggregation.png)

Profile based Start Menu taking Precedence
{:.figcaption}

`Calculator` is now missing from `Windows Accessories` despite it existing at the File System layer.

[![Common Start Menu ignored]({{site.baseurl}}/assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/NoCalculatorAggregation.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/NoCalculatorAggregation.png)

Common Start Menu ignored for calculator
{:.figcaption}

### Rule 2: The Empty Void

The Start Menu will not display empty folders. That's a lot of fun when you are testing the creation of a custom folder. The basics, I guess…

### Rule 3: Moving the Shortcuts

If you move the executable from one custom folder to another, the Start Menu will not always auto adjust on the fly if housed in the Common Start Menu Location `(%ProgramData%)`. You will need to either log off the session or kill the explorer process to see your changes. (This behaviour was for more prevalent in Windows Server 2016, Windows 10 seemed to honor these changes quickly.)

This behaviour differs in the user profile based Start Menu `(%AppData%)` which reflects these changes on the fly regardless of the OS.

[![Dynamic Aggregation]({{site.baseurl}}/assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/DynamicAggregation.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/DynamicAggregation.png)

Dynamic Aggregation
{:.figcaption}

The last image outlined below shows the Start Menu Structure once I have moved the `eventvwr` shortcut from the `%AppData%\Microsoft\Windows\Start Menu\Programs\James2`  to the `%ProgramData%\Microsoft\Windows\Start Menu\Programs\James3` Folder.

The keen eye can see that Windows is ignoring the move from `James2` to `James3` backing up the fact that changes only reflect instantly against the Profile Based Start Menu location `(_%AppData%)`.

[![Common Start Menu Ignored Again]({{site.baseurl}}/assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/DynamicAggregation2.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-custom-folders-and-shortcut-aggregation/DynamicAggregation2.png)

Common Start Menu Ignored Again
{:.figcaption}

## Summary and Lessons Learnt

More I learn the more I realize the less I know. Seems to be a relevant statement in this era of Windows 10\. I was disturbed to find post after post of people struggling with this concept, hit and miss results and even some simple feedback that was accepted as "couldn't be done". 5 bucks says everyone fell into the same trap of copying shortcuts or using common shortcuts that already existed in the Start Menu. So here are the lessons learnt:

-  Don't use inbuilt apps like notepad and calculator for testing - this will drive you batty. Use random tools like ftp.exe which don't live in the start menu by default
-  Be cognizant of any other technologies controlling or manipulating these locations. WEM and I had multiple angry words in my lab as we fought to build this out
-  Be wary of tiles. You really don't want to screw with the default locations for where you are pulling tiles from in a managed fashion (CustomStartLayout)

That's it. Until it's not.

---
layout: post
title: "Citrix WEM, Modern Start Menus and Tiles"
permalink: "/citrix-wem-modern-start-menus-and-tiles/"
description: "a love story dedicated to Modern Start Menus"
categories: [Citrix, Optimization, Start Menu, WEM, Windows, XenApp]
redirect_from: 
    - /2017/10/13/citrix-wem-modern-start-menus-and-tiles
    - /2017/10/13/citrix-wem-modern-start-menus-and-tiles/
image:
  path: /assets/img/citrix-wem-modern-start-menus-and-tiles/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## The Problem: Tiles are %^#$

There are reported challenges all over the place with the pinning of custom tiles to the start menu in Windows 10 and Server 2016 with tools such as WEM. The issue is not so much roaming them (which is a challenge in itself), it's more the completely inconsistent behaviour when trying to manipulate them. The lack of consistency and the general surrounding performance hits that attempting to control these tiles causes within environments, is the nail in the coffin for me when it comes to pinning these things.

There are numerous posts throughout the WEM forums with people struggling with issues, and a quick google search shows the global rage relating to them in general.

I looked for a way to have WEM drive the bus and provide an alternative way of managing start menu's in a semi controlled manner, providing a nicer experience in windows 10 and server 2016 without controlling tiles. I have been struggling to really understand how important it really is from a user experience, for us to control them on their behalf.

I would like to note I am not talking about roaming tiles, enough people smarter than I are dealing with this online and have documented it well, what I am talking about is manipulating these tiles on behalf of users via WEM

## Plan B: Dismiss Tiles

Here is my alternative approach - it obviously won't fit all environments, but I think given the amount of noise and pain, that it may be something worth considering. There is a reason tools like Classic Shell are still heavily utilised, and a lot of that is people simply don't care about these new "home user style tiles" and "modern" Start Menus that are being forced on us.

Please note that you will require the May Cumulative Update for Server 2016 to get past a start menu issue where your icons aren't immediately accessible [MS Article](https://support.microsoft.com/en-us/help/3198613/start-menu-shortcuts-aren-t-immediately-accessible-in-windows-server-2) and [Citrix Forum reference](https://discussions.citrix.com/topic/386579-server-2016-published-desktop-start-menu-shortcuts-not-working/)

Firstly and above all else, optimise your image and remove as much live rubbish and inbuilt fat as you can. See my [post](https://jkindon.wordpress.com/2017/09/02/image-optimization-analysis-citrix-xenapp/) around some Server 2016 options, the same applies for Windows 10 with even more benefit. The vmware optimisation tool in particular does a fantastic job if you use the [VDI Like a Pro Template](https://www.loginvsi.com/blog/520-the-ultimate-windows-10-tuning-template-for-any-vdi-environment) - but be warned, it will make admins happy - maybe not so much users. Review everything for obvious reasons. Combine this with BIS-F and ensure you kill off shared start menu apps to give you a fresh start.

Build start menu icons with WEM. It handles creating a start menu shortcut perfectly fine and can process these lightening quick. Just don't choose pin to start menu as shown below:

[![WEMShortcut]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMShortcut.png)]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMShortcut.png)

A nice little lesser known feature in WEM is the ability to create a custom start menu layout with categories as shown below. This is not tile based, this is your classic folders and icons within the start menu

[![WEMStartMenu]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMStartMenu.png)]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMStartMenu.png)

The below is an image of Windows 10 Start Menu on the left, and Windows Server 2016 on the right. Note the cleanliness. 

[![WEMDemoStartMenu]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMDemoStartMenu.png)]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMDemoStartMenu.png)

To help keep the environment in check and consistent, I enable WEM to clean-up the environment when it processes (the same can be done for taskbar shortcuts). Be careful with initial environment clean-up options, one of these physically destroys the start menu right click option, specifically it kills the WinX folders. I am trying to hunt down the specific setting that does this. 

[![WEMCleanupOptions]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMCleanupOptions.png)]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMCleanupOptions.png)

Optionally, we can disable pinning of icons to the start menu for the environment, or leave it as a user choice. I have found that when allowing users to pin their own icons, and configuring roaming appropriately, the experience is actually OK... It's when we try and control it that it falls over, which again leads me to the driving question of what is the point in me dealing with these tiles? If users want them, let them control it, else just provide them access to their apps via other methods.

Configure your profile management appropriately. I use a combination of [James Rankin](http://www.htguk.com/everything-you-wanted-to-know-about_23/) and [Carl Stalhoods](http://www.carlstalhood.com/citrix-profile-management/) profile management configurations and so far so good across both Windows 10 and Server 2016 releases (until the next release breaks it again).

Enable application existence check in WEM - this will ensure you don't post shortcuts to applications that don't exist for your users - pretty important when you start thinking about App Layering etc. as shown in my Windows 10 vs Server 2016 screen shot, the same start menu icons are published, however the apps don't exist on both builds. Luckily a quick check is completed and I only have icons for available apps.

[![WEMAppExistence]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMAppExistence.png)]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMAppExistence.png)

Flick users to the desktop rather than the start menu on logon for the best experience in a tile free world. I am assuming everyone wants to avoid the annoyance of tiles straight off the bat? This helps

[![WEMDesktopStart]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMDesktopStart.png)]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMDesktopStart.png)

Leverage desktop and taskbar pinning. I have found the taskbar pinning has been really good, this would be due to the fact it doesn't have to manipulate some absurd db file to simply pin a shortcut, and writes natively to explorer. You can pin your icons/shortcuts within WEM, and deal with different user sets as required with assignments as per normal

[![WEMTaskBarPinning]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMTaskBarPinning.png)]({{site.baseurl}}/assets/img/citrix-wem-modern-start-menus-and-tiles/WEMTaskBarPinning.png)

1.  The speed of WEM comes back. It is lightning fast to process again and your environment is flexible and importantly, consistent.
2.  You have a nice semi-governed start menu reminiscent of the days you didn't have to read through this sort of post to deal with a simple concept such as start menu management
3.  You aren't going to run into a world of pain in the next release of windows where this whole roaming chaos changes again "Touch wood"
4.  You optionally provide users the ability to control their own pinning requirements.

There is nothing worse for a user then an overzealous admin. There is nothing worse for an admin than a needy, overzealous user. There needs to be a balance here of usability for the user and control for the admin. We are often behind the eight ball in this space, however a nice middle ground is allowing some basic customisation and personalisation, my view is this is where tiles should fall.

If you must control these and define them, then go with a big bang GPO export/import logic, you can drive this via Microsoft GPO easily enough, but be sure you understand and test, and test each time a version of windows changes.

## Summary

In summary, my views are my own and I do not for a second think this will fit every environment, however there may be some where it is a perfectly good alternative. I like streamlined optimised images and the useless junk that is built into Windows 10 which all ends up as tiles instantly lands on my hit list. Tiles themselves are on my hit list.

I could well be on many users hit lists

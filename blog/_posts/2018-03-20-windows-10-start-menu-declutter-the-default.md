---
layout: post
title: "Windows 10 Start Menu: declutter the default"
permalink: "/windows-10-start-menu-declutter-the-default/"
description: "Spanking the default crud in the Windows 10 Start Menu"
categories: [BIS-F, Citrix, Start Menu, Windows]
redirect_from: 
    - /2018/03/20/windows-10-start-menu-declutter-the-default
    - /2018/03/20/windows-10-start-menu-declutter-the-default/
image:
  path: /assets/img/windows-10-start-menu-declutter-the-default/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

A short while back I [wrote a rant](http://jkindon.com/2017/10/13/citrix-wem-modern-start-menus-and-tiles/) about my dislike of the tile management in Windows 10, and my view on trying not to manage these tiles with tools such as Workspace Environment Management.

I have had some fun across a few projects since I wrote that post, and my view on managing them combined with some of the changes in the later OS releases, has eased a little, however I still find myself consistently drawn towards the Windows 10 LTSB look and feel. In my humble opinion It's the way Windows 10 should look and feel in the enterprise and is a pleasure to work with compared to the fluff that ships with Windows 10 Pro and Enterprise in Current Branch releases.

There is lots of nice new functionality and capability in the current releases of windows 10 that enterprises can enjoy so I continue to try and get the user experience as close to LTSB with Current Release Windows 10 deployments as I can, rather than what is given to us by default

[![Win10_Default]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Win10_Default.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Win10_Default.png)

Windows 10 1709 Enterprise Default Start Menu
{:.figcaption}

There is nothing ground breaking in this post, just a collection of configurations I use to try and strip windows back a little. Also note, that I am limiting this to Windows 10 Enterprise capability, if you are on Windows 10 Pro then this stuff isn't all that relevant, and you should go and carefully have a look at the [Windows 10 decrapifier script](https://community.spiceworks.com/scripts/show/3977-windows-10-decrapifier-version-2)

## Removing Modern Applications

Modern Applications, AppX Packages, Universal Windows Platform (UWP) Apps - whatever you want to call them, tend to often not be wanted or required in Enterprise VDI deployments.

The default behaviour of these things to be provisioned within the user context, is somewhat invasive and frustrating when trying to build out a controlled and manageable environment. To Microsofts credit, LTSB is their answer to the above frustrations, however many organisations don't have access to LTSB, and some simply want the latest code base available.

To address this issue somewhat, my starting point is to run the Citrix Optimizer Tool across your 170x image and select to remove Built-in Apps. Why use the tool? 1) Its awesome 2) its supported by Citrix to do so.

[![Optimizer]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Optimizer.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Optimizer.png)

This approach will remove the applications from the default user profile, however will not touch existing profiles already created, including the profile you are running the tool under. All new profiles will no longer receive these modern applications (except for Edge. Read on.)

NB: There are some new UWP apps as part of Windows 1709 that simply don't want to go anywhere. I haven't delved down far enough to figure out the "why" of this yet. There are an additional set of Modern applications that are not dealt with using the Citrix Optimizer tools, these are known as Consumer Content. Without dealing with these, your profiles created from now on will look as below:

[![Win10Optimise]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Win10Optimise.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Win10Optimise.png)

## Disable Consumer Content, Disable Windows Tips and Disable Cortana

To this day, I cannot fathom why this fantastic selection of Enterprise-Appropriate goodies is even a part of an Enterprise grade operating system. But neither can most people, so I won't rant on, instead I just remove them via GPO:

*Computer Configuration > Administrative Templates > Windows Components > Cloud Content > Turn off Microsoft consumer experience (Enabled)*

I also see zero point in Windows providing me Tips, so I remove them via GPO also:

*Computer Configuration > Administrative Templates > Windows Components > Cloud Content > Do not show Windows tips (Enabled)*

I also have disabled Cortana:

*Computer Configuration > Administrative Templates > Windows Components > Search > Allow Cortana (Disabled)*

With the above settings configured, new profiles now looking like the below:

[![Win10Consumer]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Win10Consumer.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Win10Consumer.png)

One more piece to go: Microsoft Edge.

## Remove the remaining Tile - Edge

In the 1709 build, the last piece of the puzzle is Microsoft Edge. I have no problem with Edge as a browser, however I do have a problem with it being shoved on my start menu after I remove everything else. I am sure there is a nicer way to remove it (seriously if there is tell me) but at the moment I am resorting to a nice little PowerShell snippet to remove it if detected on the Start Menu:

```powershell
((New-Object -Com Shell.Application).NameSpace('shell:::{4234d49b-0245-4df3-b780-3893943456e1}').Items() | where-object {$_.Name -eq "Microsoft Edge"}).Verbs() | where-object {$_.Name.replace('&','') -match 'From "Start" UnPin|Unpin from Start'} | %{$_.DoIt()}
```

This can be run in the form of a Logon Script, a scheduled task to run at each logon, or a WEM external task. Run it as you see fit. I have seen some instances where due to delays in logon script processing etc, you may need to run it twice before you have the desired effect. I have been able to address this with a simple "sleep and repeat" logic in PowerShell. 

[![Win10Edge]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Win10Edge.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Win10Edge.png)

If you do this, then decide you want to manage tiles later, and edge happens to be one of those tiles then remove the logon script. 

**Edit 24.04.2018** [Mans Hurtigh](https://discussions.citrix.com/profile/12582752-m%C3%A5ns-hurtigh/) over on the [Citrix Forums](https://discussions.citrix.com/topic/394711-w10-1709-ent-start-menu-and-tiles/) has offered an alternative way to remove the remaining icon using effectively a blank Start Menu Layout - I have not had the chance to test it yet, however this is a nice alternative to dealing with Edge 
{:.special_note}

## Common Programs

Now we have dealt with tiles, lets strip back the start menu even more and clean out what we don't want users to see. Much of this content comes from the `%ALLUSERS%` profile, which is conveniently located here: `C:\Programdata\Microsoft\Windows\Start Menu\Programs`

Now here is a disclaimer. If you plan to deploy custom start menu layouts in the future (pinning tiles), do not block access to the above location, as that's where your tiles primarily come from. If you aren't planning on doing so, then the following GPO should clean up some clutter for you
{:.warning}

*User Configuration > Administrative Templates > Start Menu and Taskbar > Remove Common program groups from the Start Menu (Enabled)*

[![GPCommonPrograms]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/GPCommonPrograms.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/GPCommonPrograms.png)

Alternatively, you can use a GPO to remove read access for "users" from this location, or specific folders within this location if you need some content, but not all content to be removed. 

Citrix WEM also provides an option to drive this setting named **"Hide Common Programs"** 

[![WEMCommonPrograms]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/WEMCommonPrograms.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/WEMCommonPrograms.png)

The result is substantially cleaner, and well ready for you to start populating with relevant content:

[![Win10CommonPrograms]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Win10CommonPrograms.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/Win10CommonPrograms.png)

## Default User Profile

The remaining items appearing in the Start Menu which are not addressed by the above, will typically land based upon what lives in the default user profile, located here: `C:\Users\Default\AppData\Roaming\Microsoft\Windows\Start Menu\Programs`

[![DefaultUsers]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/DefaultUsers.png)]({{site.baseurl}}/assets/img/windows-10-start-menu-declutter-the-default/DefaultUsers.png)

Removing content from this location will ensure that the content does not appear for all new user profiles. For those profiles that already exists and have the contents copied over to start with, you can delete with GPO or WEM using file based actions

## Final Thoughts

Run BIS-F to seal your image, then go enjoy your LTSB looking windows 10 1709 Image. 

Oh and add some start menu icons with WEM or GPP - else you maybe have some weirded out users with almost empty start menus.

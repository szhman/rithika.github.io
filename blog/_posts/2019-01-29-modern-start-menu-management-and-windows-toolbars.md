---
layout: post
title: "Modern Start Menu Management and Windows Toolbars"
permalink: "/modern-start-menu-management-and-windows-toolbars/"
description: "An alternate approach to the standard Start Menu manipulation"
categories: [Apps and Desktops, Citrix, Start Menu, WEM, Windows, XenApp]
redirect_from: 
    - 2019/01/29/modern-start-menu-management-and-windows-toolbars
    - 2019/01/29/modern-start-menu-management-and-windows-toolbars/
image:
  path: /assets/img/modern-start-menu-management-and-windows-toolbars/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

The Windows 10 Modern Start Menu has introduced a range of challenges and frustrations for end users and admins alike, particularly within environments that operated for long periods with a typical Windows 7 or Windows Server 2008 R2 redirected Start Menu, a technique which no longer lends itself well to the new modern Start Menu. Learning to accept, manipulate, alter and manage the Windows 10 / Windows Server 2016 Start Menu is one of an EUC admins biggest challenges, not just in the technology side of things, but in user acceptance also.

I have written previously about [managing the Start Menu Layouts](https://www.citrix.com/blogs/2018/04/10/customizing-the-windows-10-start-menu-to-enable-better-control-better-experience/), [cleaning the base Start Menu clutter](https://jkindon.com/2018/03/20/windows-10-start-menu-declutter-the-default/), and general frustration with [Tiles & Modern Applications](https://jkindon.com/2017/10/13/citrix-wem-modern-start-menus-and-tiles/) and have spent enough time with these solutions and configuration options that they are second nature, however many environments coming out of the Windows 7 and Windows Server 2008 R2 era continue to struggle with the enormous shift to Windows 10 and Windows Server 2016 or 2019.

One of the biggest changes that occurred in the Windows Start Menu (left hand side, forget tiles), is the change in how Windows displays icons and folder structures. There is a limit of 1 folder deep under the "Programs" folder, where the Start Menu will only show you a single folder, with all sub folders ignored, and their icons aggregated into the root folder, an example below

The Browsers Folder contains a file structure on the Operating System of:
`%AppData%\Microsoft\Windows\Start Menu\Programs\Browsers\Google`
`%AppData%\Microsoft\Windows\Start Menu\Programs\Browsers\Microsoft`
`%AppData%\Microsoft\Windows\Start Menu\Programs\Browsers\Mozilla`

[![Explorer View of the Start Menu]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/ExplorerView.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/ExplorerView.png)

Explorer View of the Start Menu
{:.figcaption}

Each with a corresponding application: Chrome, Internet Explorer and Firefox accordingly

However, the Windows 10 Start Menu will display the applications "Chrome, Internet Explorer and Firefox" icon under the root "Browsers" Folder, ignoring the remaining sub folders

[![Start menu View of the Browser Folder]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Start-BrowserView.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Start-BrowserView.png)

Start menu View of the Browser Folder
{:.figcaption}

This can pose a large acceptance challenge to the end user base when moving from legacy Operating Systems. Admins have typically fallen back to solutions such as classic shell to reskin the Start Menu and make it legacy again, which whilst acceptable, doesn’t lend well to future upgrades and changes that may be implemented. Furthermore, once you adapt to the modern start menu, and configure it appropriately for roaming and customisation, its actually quite efficient.

I often talk to customers about adoption of modernisation and our discussions will regularly lead to a misconception that all users are thick, and do not want to change from the day to day of what they do. This is not typically accurate these days, Microsoft and the force feeding of new ways of working via the consumer offerings have stamped a mentality of change on people, where Windows 10 is simply accepted and commonplace in the average home, so the requirement to force the EUC environment in a corporate environment to look legacy is far less than many believe.

Saying that, those environments that have operated via a redirected or deeply structured Start Menu in legacy environments will struggle with this change, more from a perspective of users being lost on where to find their applications rather than a technical view, which is why its important to understand the available tools to help. One such method being the use of Toolbars in Windows, a hidden gem when it comes to Start Menu adoption in the modern era.

## Deploying the Solution

A custom Toolbar displays the underlying folder structure as the file system views it. Completely different to the way the modern Start Menu reads and displays this information, which given that the Start Menu folder is still logically structured and managed at a file system level, makes it the perfect addition to the arsenal when moving to the new world, providing both administration and user experience ease.

Deploying a custom toolbar however is not as simple as one might hope, however very much achievable once you know where to look.

Create the custom Toolbar in a standard user profile.

[![Create a new Toolbar]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Toolbar-Create.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Toolbar-Create.png)

Create a new Toolbar
{:.figcaption}

Point the toolbar to `%AppData%\Microsoft\Windows\Start Menu\Programs`, this expands to `C:\Users\%UserName%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs`

[![Toolbar File System Location]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Toolbar-FileSystemLocation.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Toolbar-FileSystemLocation.png)

Toolbar File System Location
{:.figcaption}

The Toolbars Menu will now display our new Programs Toolbar, with the Taskbar shortcut to the toolbar auto created

[![Created Toolbar]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Toolbar-Created.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Toolbar-Created.png)

Created Toolbar
{:.figcaption}

If we look at the `Browsers` Folder via the Toolbar, we can now see the full file system path of the application itself, a very familiar experience for the end user

[![Toolbar Browser Folder View]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Toolbar-BrowserView.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Toolbar-BrowserView.png)

Toolbar Browser Folder View
{:.figcaption}

If we look at the `WEM Tools` Folder via the Start Menu, we we see a consoldated view of the icons

[![Start Menu WEM Tools View]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Start-WEMView.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Start-WEMView.png)

Start Menu WEM Tools View
{:.figcaption}

However, the Toolbar honours the folder structure:

[![Toolbar WEM Tools View]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Toolbar-WEMView.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/Toolbar-WEMView.png)

Toolbar WEM Tools View
{:.figcaption}

Toolbar configurations are stored in the registry as binary entry under the following key:

> `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Streams\Desktop`

Lucky for us this key is in the HKCU user hive, which means we can now get selective in handling deployments and alterations on a per user basis.

I recommend creating a custom toolbar on the system you plan to deploy it on, this isn’t a hard and fast rule, but as with most things EUC based, better safe than sorry. From there you can export the registry entry.

[![Toolbar Registry Location]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/RegLocation.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/RegLocation.png)

Toolbar Registry Location
{:.figcaption}

Save the file as toolbar.reg, and copy it to a central location that is accessible to all users, for this example the reg file ended up living in the NETLOGON share

> `\\Kindo.com\Netlogon\Toolbar\ToolBar.reg`

The registry value applies after an explorer process restart, so you can either log off the user after its been imported or you are going to need to kill the explorer process in the user context. I am using PowerShell to achieve this in the example below, however you can utilise whatever language tickles your fancy

```powershell
Start-Process -filepath "reg.exe" -argumentlist "import \\Kindo.com\Netlogon\Toolbar\ToolBar.reg"

taskkill.exe /F /IM explorer.exe

Start-Process Explorer.exe
```

Save the script to a central location that is accessible to all users.

No surprises that I am driving this example with Citrix Workspace Environment Management (Which can also build out your Start Menu structures at the file system layer), using an external task. However once again choose your poison, Group Policy etc 

[![WEM Start Menu Layout]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/WEMStartMenu.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/WEMStartMenu.png)

WEM Start Menu Layout
{:.figcaption}

Profile Management will roam the toolbar with the user profile once created, so you may choose to make this a run once action, and run only at logon 

| **WEM Conf** | **Detail**
| **Name** | `Create Start Menu Toolbar`
| **Description** | `Create Start Menu Toolbar`
| **Path** | `Powershell.exe` 
| **Arguments** | `-ExecutionPolicy Bypass -File \\Kindo.com\Netlogon\Toolbar\CreateToolbar.ps1`

[![WEM External Task]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/WEMTask.png)]({{site.baseurl}}/assets/img/modern-start-menu-management-and-windows-toolbars/WEMTask.png)

WEM External Task
{:.figcaption}

If you need to rerun this at any time, you can use my [WEM Tracking Cache Reset utility](https://jkindon.com/2018/11/28/selective-deletion-of-the-wem-actions-tracking-cache/) to reset external tasks tracking.

All users assigned the action should now have a Programs Tool bar created based on the `%AppData%\Microsoft\Windows\Start Menu\Programs` File System location

## Summary

End user Computing is all about user experience and user acceptance. This doesn’t mean throwing the baby out with the bathwater when it comes to adopting new services and experiences just because there is a significant change, and it doesn’t always mean throwing in additional software to hide or alter the out of box capability of a Modern Operating System.

We are challenged as architects, consultants and Engineers in designing a transition to these new modern "views", and the simple use of toolbars is yet another weapon in the arsenal associated with balancing user expectations, productivity and adoption of new ways of working all at the same time.

Simplicity, as always is a thing of beauty

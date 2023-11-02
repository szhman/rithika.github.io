---
layout: post
title: "Studio Apps to WEM to address Citrix Session Sharing challenges"
permalink: "/studio-apps-to-wem-to-address-citrix-session-sharing-challenges/"
description: "Mitigating Receiver fails with WEM Studio App Intgegration"
categories: [Apps and Desktops, Citrix, Start Menu, WEM, Windows, XenApp]
redirect_from: 
    - /2019/03/23/studio-apps-to-wem-to-address-citrix-session-sharing-challenges
    - /2019/03/23/studio-apps-to-wem-to-address-citrix-session-sharing-challenges/
image:
  path: /assets/img/studio-apps-to-wem-to-address-citrix-session-sharing-challenges/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

I recently engaged with a customer who had chosen to deploy Citrix 7.15 LTSR across multiple sites utilising Windows Server 2016 published desktops as their primary delivery platform. The user base had been a long term consumer of Windows Server 2008 R2 based published desktops, and the IT teams had utilised the typical management technique to control the user environment, Start Menu redirection, access based enumeration with fine grained NTFS permission assignments to control who can see what in the Start Menu etc.

This approach worked for many organisations until the introduction of a Modern Start Menu from Microsoft, a topic I have discussed multiple times over the last few years.

The decision has been made to utilise receiver to publish applications into the Start Menu in an attempt to introduce some level of Start Menu management into the environment, however being on the LTSR platform, the functionality associated with [session sharing of Apps and Desktops](https://www.citrix.com/blogs/2018/03/08/session-sharing-between-a-published-desktop-and-a-published-application-made-easy/) is not really ideal. In fact the [work around](https://support.citrix.com/article/CTX218060) required to make this work is unnecessarily complex, full of management overhead and multiple touch points and adds negative impact to session logon times.

This challenge is obviously remediated via the [vPrefer](https://support.citrix.com/article/CTX232210) feature in 7.17, but that doesn’t help LTSR customers.

The solution? Workspace Environment Management (again). WEM is more than capable of delivering a robust and dynamic Modern Start Menu without any of the logon impact overhead or multiple touch points. I have written on how to control the [Start Menu with WEM](https://jkindon.com/2017/10/13/citrix-wem-modern-start-menus-and-tiles/) so won't double tap into how this is done, however we had a nice challenge in moving a few hundred applications out of the Citrix Studio and into WEM. Currently there is no functionality to delivery this via native tools, and it's a manual process. Or at least ***was*** a manual process until my good friend [Arjan Mensch](https://msfreaks.wordpress.com/author/arjanmensch/) stepped in once again to save the day with another awesome addition to his [WEM PowerShell Module](https://msfreaks.wordpress.com/2019/01/25/powershell-module-for-citrix-wem-part-4-import-published-applications/).

The basic premise is to provide an export of all published applications in a CSV format to the module, which then creates an application based action file for import to WEM. Beautiful. One could argue that it would be slightly more automated to have the module pull the list of apps directly from Virtual Apps, however the entire module is currently standalone with no requirements for anything else which is the way we like it.

An additional consideration that we needed to cater for was multiple farms being aggregated into storefront and published into the Start Menu. This was addressed by the [Storefront application type](https://jkindon.com/2018/03/28/storefront-resource-integration-with-wem-4-6/) that WEM provided as of 4.6.

## Summary

Another more than effective outcome for a dirty problem. Customer now has faster logon times, reduced interactive logon times (receiver), reduced complexity, single management pane and a huge amount of flexibility all whilst not having to hack and crack anything to get things going. And before anyone comments around Tiles, we used a Custom Start Layout :)

---
layout: post
title: "WEM Hydration Kit Module – Environmental Settings to Registry Actions"
permalink: "/wem-hydration-kit-module-environmental-settings-to-registry-actions/"
description: "A bolt on to the WEM Hydration Kit allowing environmental settings to be selectively deployed"
categories: [Citrix, Start Menu, WEM, Windows, XenApp]
redirect_from: 
    - /2019/01/02/wem-hydration-kit-module-environmental-settings-to-registry-actions
    - /2019/01/02/wem-hydration-kit-module-environmental-settings-to-registry-actions/
image: 
  path: /assets/img/wem-hydration-kit-module-environmental-settings-to-registry-actions/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

One of the limitations of WEM based environmental settings is that they are all or nothing from an assignment perspective, meaning that when you enable them, outside of excluding administrators, everyone is going to be hit by that setting. This isn't always ideal for environments that want selective assignments of these particular settings. The settings I am talking about are located below:

[![WEM Environmental Settings]({{site.baseurl}}/assets/img/wem-hydration-kit-module-environmental-settings-to-registry-actions/EnvSettings.png)]({{site.baseurl}}/assets/img/wem-hydration-kit-module-environmental-settings-to-registry-actions/EnvSettings.png)

Luckily, as with most things with WEM, where this is a will there is a way, and delving back to the simplicity that WEM is built on, we know that each of these settings is simply a user based registry key….which means using Registry actions, we can be as selective and fine grained as we like…

Citrix released a reference article [here](https://docs.citrix.com/en-us/workspace-environment-management/current-release/reference/environmental-settings-registry-values.html), most of which we already known from years of playing in the Microsoft world, however they made it nice and easily matched to each of the settings they define in the GUI.

As such, I have created a new module for the [Hydration Kit](https://github.com/JamesKindon/WEMHydrationKit) with all settings defined as Registry actions (yes it was painful), allowing you to import directly in to your existing environment. As per usual I have also updated the master Kit module to include these settings for all new environments. The description in each entry will tell you what you need to set, and all actions have been created as enabled, with apply once disabled.

[![Imported Registry Actions]({{site.baseurl}}/assets/img/wem-hydration-kit-module-environmental-settings-to-registry-actions/Import.png)]({{site.baseurl}}/assets/img/wem-hydration-kit-module-environmental-settings-to-registry-actions/Import.png)

You may note that some of these actions look familiar in the existing base Hydration master. Some are, however some are subtly different and provide a nicer approach to how WEM handles lockdown of certain things at times. These are documented within the inventory notes and I am leaving them in the master

Download Link is [here](https://github.com/JamesKindon/WEMHydrationKit)

Happy New Year :)

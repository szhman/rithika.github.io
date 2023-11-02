---
layout: post
title: "Citrix UPM module for the WEM Hydration Kit"
permalink: "/citrix-upm-module-for-the-wem-hydration-kit/"
description: "UPM configurations added to the hydration kit"
categories: [Citrix, UPM, WEM]
redirect_from: 
    - /2017/11/27/citrix-upm-module-for-the-wem-hydration-kit
    - /2017/11/27/citrix-upm-module-for-the-wem-hydration-kit/
image:
  path: /assets/img/citrix-upm-module-for-the-wem-hydration-kit/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

If you are choosing to drive Citrix UPM configurations from WEM, then you can now consume as part of the Hydration Kit, a full configuration based on my current UPM build for Windows 10/Server 2016.

These settings are a combination of [James Rankin](http://www.htguk.com/everything-you-wanted-to-know-about_23/) and [Carl Stalhood](http://www.carlstalhood.com/citrix-profile-management/) and to date have been roaming Windows 10 and Server 2016 well for me. However, they may need tweaking across your own environments and specific flavours of Windows 10/Server 2016

To note in this configuration

-  Active Write-Back is disabled on purpose. Only enable this if you have a specific need to do so
-  Basic Logging is enabled to the default locations – change if you need
-  I don’t migrate existing profiles on principle, so my configuration reflects this – change if you need to
-  I delete local in the case of conflicting – change if you need to
-  I have disabled UPM for admins – change if you need to
-  This template assumes you use Microsoft folder redirection.

Review all configuration settings and Test before pushing into production. This is a starting baseline and not something I claim to be perfect for all environments

Download link for the Kit [here](https://github.com/JamesKindon/WEMHydrationKit)
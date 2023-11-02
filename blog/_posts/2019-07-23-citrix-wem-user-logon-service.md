---
layout: post
title: "Citrix WEM User Logon Service"
permalink: "/citrix-wem-user-logon-service/"
description: "Killing off useless delays caused by the WEM User Logon Service"
categories: [Apps and Desktops, Citrix, Cloud, Optimization, WEM, Windows, XenApp]
redirect_from: 
    - /2019/07/23/citrix-wem-user-logon-service
    - /2019/07/23/citrix-wem-user-logon-service/
image:
  path: /assets/img/citrix-wem-user-logon-service/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Back in the 1812 release of the `Citrix WEM Service Agent`, Citrix released an additional Windows Service named `Citrix WEM User Logon Service` which started showing up during the logon process with a slight delay. Fast forward to the 1906 release of WEM, and that same service exists and now clearly displays a significant delay when user logons occur.

The reason for this is due to what this process actually does behind the scenes, which isn't documented anywhere at this point.

Citrix released an integration between the WEM Service with CEM (Citrix Endpoint Management) a while back, which allows CEM to push Group Policy Settings to the WEM Service, which then applies these policies via the WEM agent combined with the `Citrix WEM User Logon Service` on the endpoint. Pretty neat really (I haven't done it), but to achieve this, the `Citrix WEM User Logon Service` effectively adds a ~5 second delay to the logon experience so that it can process those policy settings. You can read more on the integration and configuration at the following: [Windows GPO Configuration Policy](https://docs.citrix.com/en-us/citrix-endpoint-management/policies/windows-gpo-configuration-policy.html)

This obviously makes absolutely no sense in a traditional WEM or WEM Service environment which has GPO's applied via normal methods, and as such, needlessly adds a nasty delay to your logons that simply doesn't need to be there. It is perfectly safe to stop this service, and change its start-up type to manual, which will then bring your logon speeds back to where they should be.

The WEM development team have dignified this challenge, and are working on not just stopping this from happening in normal environments, but also in reducing the delay introduced to process the ADMX/policy settings configured via CEM in a future release.

What intrigues me now, is that via CEM we can deploy native ADMX configurations to endpoints via the WEM agent - will this ability be extended out to normal WEM environments? That would have to be one of the most requested features for WEM to date…..who knows

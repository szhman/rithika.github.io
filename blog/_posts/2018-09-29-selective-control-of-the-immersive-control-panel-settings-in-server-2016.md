---
layout: post
title: "Selective Control of the Immersive Control Panel (Settings) in Server 2016"
permalink: "/selective-control-of-the-immersive-control-panel-settings-in-server-2016/"
description: "Getting hold of the immersive control panel in Server 2016"
categories: [Citrix, Start Menu, WEM, Windows, XenApp]
redirect_from: 
    - /2018/09/29/selective-control-of-the-immersive-control-panel-settings-in-server-2016
    - /2018/09/29/selective-control-of-the-immersive-control-panel-settings-in-server-2016/
image:
  path: /assets/img/selective-control-of-the-immersive-control-panel-settings-in-server-2016/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

It has been a considerable amount of time that the inability to nicely manage the Immersive Control Panel or "Settings" App provided in place of the traditional Control Panel in Server 2016 has frustrated Citrix and RDS admins. Finally as of the September Cumulative Update for Server 2016, we can selectively lock this down in a similar fashion that we can with the traditional Control Panel.

Credit goes out to [sklopp](https://discussions.citrix.com/profile/12627556-sklopp/) at [Citrix discussions](https://discussions.citrix.com/topic/398241-server-2016-pc-settingsimmersive-control-panel) who brought this to light.

The official release details can be found [here](https://support.microsoft.com/en-us/help/4457127/windows-10-update-kb4457127)

## The Management Options

Microsoft provide an ADMX option to lock this down which Carl has already documented [here](https://www.carlstalhood.com/group-policy-objects-vda-user-settings/#settingspage). Alternatively you can write the HKCU keys with whichever VUEM tickles your fancy, or via good old GPP with Item level targeting.

It is nice to see that there is a specific `Show Only` and well as a `Hide Only` option when controlling this.

A list of applets (can we call them that?) is outlined below

| `display`| `emailandaccounts` | `extras` | `findmydevice` | `lockscreen` | `Maps`
| `mousetouchpad` | `network-ethernet` | `network-cellular` | `network-mobilehotspot` | `network-proxy` | `network-vpn`
| `network-directaccess` | `network-wifi` | `notifications` | `nfctransactions` | `easeofaccess-narrator` | `easeofaccess-magnifier`
| `easeofaccess-highcontrast` | `easeofaccess-closedcaptioning` | `easeofaccess-keyboard` | `easeofaccess-mouse` | `easeofaccess-otheroptions` | `optionalfeatures`
| `otherusers` | `powersleep` | `printers` | `privacy-location` | `privacy-webcam` | `privacy-microphone`
| `privacy-motion` | `privacy-speechtyping` | `privacy-accountinfo` | `privacy-contacts` | `privacy-calendar` | `privacy-callhistory`
| `privacy-email` | `privacy-messaging` | `privacy-radios` | `privacy-backgroundapps` | `privacy-customdevices` | `privacy-feedback`
| `recovery` | `regionlanguage` | `storagesense` | `tabletmode` | `taskbar` | `themes`
| `troubleshoot` | `typing` | `usb` | `signinoptions` | `sync` | `workplace`
| `windowsdefender` | `windowsinsider` | `windowsupdate` | `yourinfo`

### Selective Control with WEM

Because I like to do everything with WEM where I can, this article will cover the configuration at a WEM level, however GPP and Item Level Targeting can achieve the same thing for other environments.

I like this approach because you can be as selective as you like by writing the Current User Keys directly.

Create the WEM Registry Action:

| **Name** | `Windows Settings (ICP) - Show Only Display`
| **Description** | `Immersive Control Panel - Shows Display`
| **Key** | `SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer`
| **Type** | `REG_SZ`
| **Value** | `SettingsPageVisibility`
| **Data** | `Showonly:display`

[![WEMAction]({{site.baseurl}}/assets/img/selective-control-of-the-immersive-control-panel-settings-in-server-2016/WEMAction.png)]({{site.baseurl}}/assets/img/selective-control-of-the-immersive-control-panel-settings-in-server-2016/WEMAction.png)

You can then assign the action to whoever you want based on whatever conditions you want as per normal

## The Result

Below is my user logged into a Server 2016 image that does not have the latest September patch applied

[![NonPatchImage]({{site.baseurl}}/assets/img/selective-control-of-the-immersive-control-panel-settings-in-server-2016/NonPatchImage.png)]({{site.baseurl}}/assets/img/selective-control-of-the-immersive-control-panel-settings-in-server-2016/NonPatchImage.png)

And below, is the same user logged into an identical build, but with the update deployed

[![PatchedImage]({{site.baseurl}}/assets/img/selective-control-of-the-immersive-control-panel-settings-in-server-2016/PatchedImage.png)]({{site.baseurl}}/assets/img/selective-control-of-the-immersive-control-panel-settings-in-server-2016/PatchedImage.png)

And now my admin user logged in - no restrictions

[![PatchedImage-Admin]({{site.baseurl}}/assets/img/selective-control-of-the-immersive-control-panel-settings-in-server-2016/PatchedImage-Admin.png)]({{site.baseurl}}/assets/img/selective-control-of-the-immersive-control-panel-settings-in-server-2016/PatchedImage-Admin.png)

Pretty happy with that result.

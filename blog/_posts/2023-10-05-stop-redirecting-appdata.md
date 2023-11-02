---
layout: post
title: "Stop Redirecting AppData. It's no longer the 80's"
permalink: "/stop-redirecting-appdata/"
description: "It's 2023 Stop Redirecting AppData in EUC environments"
categories: [Citrix, Cloud, VDI, DaaS, EUC, Microsoft, UEM]
image:
  path: /assets/img/stop-redirecting-appdata/post_default_image.jpg
sitemap: true
hide_last_modified: false
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

It's 2023 and yet we are still seeing (regularly), consultants and customers implementing AppData folder redirection into modern EUC deployments. The most common justification: `We did it on legacy OS, why not do it on the new one`. 🤯

Firstly, let's address that. AppData redirection on legacy OS was bad behaviour. The reason it didn't impact as badly as it does now, is that modern applications tend to write more data into AppData, and those modern applications aren't typically deployed on legacy Operating Systems.

In this day and age, AppData redirection is significantly frowned upon and is a key component of badly performing environments. Imagine the impact on file servers when you have services like Microsoft Teams, Slack, Office Temp files, Application logs, etc. all pounding a File Server? Nothing good comes from this.

What's even more mind-boggling, is when customers implement container technology for their profile solution and then redirect AppData out of it and onto a file share. Madness.

AppData redirection was brought in as an option to centralise data when multiple machines/sessions may have been used. The reality is, that the use case has well and truly dwindled off over time, and better methodologies have been introduced to handle the scenario where data is required across multiple sessions.

AppData is a pretty dirty dumping ground these days. With file-based profile solutions, you need to tune them to remove the crud and reduce the amount of useless rubbish roaming around (which will impact logon and logoff times). With container technologies, most customers are trying to exclude or redirect the data out of the container so it's not retained.

Folder Redirection as a whole is not a new talking point. Aaron Parker, Helge Klein and Shawn Bass had a presentation on this back in 2015. That is almost `10 years ago`. Aaron has [documented the series here](https://stealthpuppy.com/folder-redirection-2015-part-1/) for anyone who is interested.

There are some use cases that make sense for Folder Redirection, be it to a network location, or to something like OneDrive, these are primarily the users `desktop` for consistency and because it's often the first thing they see, and `documents` as it tends to be the default save location for most things by default, and is commonly used by users.

Pretty much everything else is a waste of time.

Here is a list of what can be redirected, vs. what should be considered for redirection:

| Component | Yes/No | Detail |
| :-- | :-- | :-- |
| AppData/Roaming | ❌ | It's 2023. Stop it |
| Contacts | ❌ | Has anyone used this in the history of Windows? |
| Desktop | 🤔 | Good option for consistency |
| Documents | 🤔 | Good option for consistency |
| Downloads | ❌ | Not sure why this data should be retained in any environment |
| Favorites | ❌ | It's 2023. Stop using IE |
| Links | ❌ | This is from the days before man. No need for this. |
| Music | ❌ | Are you really storing users music files? |
| Pictures | 🤔 | Are you really storing users picture files? |
| Saved Games | ❌ | I don't even need to address this |
| Searches | ❌ | Don't do this |
| Start Menu | ❌ | Definitely do not do this unless you enjoy pain |
| Videos | ❌ | Are you really storing users video files? |

## A Note on Client Side Extension Impacts

As outlined by mr [James Rankin](https://james-rankin.com/articles/make-citrix-logons-use-asynchronous-user-group-policy-processing-mode/), use of Group Policy to push Folder Redirection Policies can force your GPO processing into Synchronous Mode which impacts logon times. So even if you reverse the settings via GPO, you are still going to be hitting this challenge (see script below). If you are a Citrix customer and still need something like desktop or documents redirected, then Citrix Policy does not use a CSE and as such does not impact GPO processing, nor does Citrix WEM configurations. Food for thought.

## Summary

Please stop implementing legacy methodology into modern deployments. It hurts the user experience, it impacts file servers and other services relying on them, it can hurt the application performance, and it is dead logic.

If you want to revert folder redirection without having to use the `revert back to local` policy settings, you can use a [small script I have here](https://github.com/JamesKindon/Citrix/blob/master/SetShellFolderDefaults.ps1) to help right the wrongs.

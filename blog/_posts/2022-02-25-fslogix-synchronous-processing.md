---
layout: post
title: "FSLogix Synchronous Processing"
permalink: "/fslogix-synchronous-processing/"
categories: [Citrix, FSLogix, Profiles, Windows, CVAD, GPO]
description: Got slow logon times with FSLogix? Hello Synchronous GPO processing
image: 
  path: /assets/img/fslogix-synchronous-processing/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

**Preface:** This article is a living breathing article as we delve more into the why, how and expected behaviours of GPO processing, not just with an FSLogix lens. I will be updating, amending and altering content as more information arises. 
{:.warning}

## Intro

For those on the FSLogix profile management path, there are some nasty behaviours you may be experiencing as it relates to Group Policy processing modes. The long and short, FSLogix is enforcing **Synchronous** processing for all logons rather than allowing and enabling **Asynchronous** processing like it has in the past. If you aren’t sure on why this is a big deal, have a read of [James Rankins](https://twitter.com/james____rankin) article [Make Citrix logons use asynchronous user Group Policy processing mode](https://james-rankin.com/articles/make-citrix-logons-use-asynchronous-user-group-policy-processing-mode/).

This is not a new behaviour; however, it is undocumented (to the best of my knowledge) and it was not communicated which is not amazing. A change was introduced with FSLogix Apps 2.9.7654.46150 to allow Async Processing. A version prior to this (specifically 2.9.7349.30108 at this stage) allowed Group Policy state roaming, effectively telling Windows that "*no, this is not the first logon, use async processing*".

The support information I have from a customer direct from Microsoft is as follows:

> *The current functionality is expected. Unfortunately, the logon may take longer in the newer FSLogix version, but this is how we do as FSLogix users should be treated as “FirstLogon" every time in non-persistent VDI environment, and the slow logon is expected because the user needs to be applied GP synchronously on every logon. <br><br> The previous FSLogix version(2.9.7349.30108) had some wrongly optimization on GP processing as it caused regression, so we need to revert back in the later version. The FSLogix behavior has historically always been to not roam any GP state. We attempted to add some optimizations for this, but this optimization caused some GP issues so that change was reverted. <br><br> This is currently working as designed from FSLogix perspective.*
{:.per_microsoft}

In the ideal state, the first time we logon we will have an expected Synchronous process for GPO as shown below

[![sync_bad]({{site.baseurl}}/assets/img/fslogix-synchronous-processing/sync.png)]({{site.baseurl}}/assets/img/fslogix-synchronous-processing/sync.png)

However, a second logon to the same box should result in Asynchronous processing as below:

[![async_good]({{site.baseurl}}/assets/img/fslogix-synchronous-processing/async.png)]({{site.baseurl}}/assets/img/fslogix-synchronous-processing/async.png)

## How did it work before?

It would appear the secret sauce (and I am making an educated guess here) was that previously the `state` keys for the user was migrated and injected by FSLogix into HKLM, allowing the machine to understand that this was not the first logon, even in a non-persistent environment, specifically:

> `HKLM\Software\Microsoft\Windows\CurrentVersion\Group Policy\State\user_SID`

**07.03.2022**: Functionality confirmation
{:.special_note}

With version 2.9.7349.30108, FSLogix introduced some new capability which appears to have actioned the following:

-  On user logoff, export the HKLM based `state` and `sid` key for the current user into the local profile. This file appears to have been encrypted. It was stored under `C:\Users\%UserName%\AppData\Local\FSLogix`
-  On user logon, import the `state` key back into the system, which would trick the OS into thinking that this was not the first logon, and async processing would occur. This was actioned as soon as the VHD attach was successful

This process can clearly be seen in the FSLogix logs. This no longer occurs, hence every logon is a new logon.

This has been updated on [James Rankins initial post](https://james-rankin.com/articles/make-citrix-logons-use-asynchronous-user-group-policy-processing-mode/) as it was the most fitting place for the information to live.

## Where to from here?

As mentioned, this is not new. I am typically involved in deployments where we leverage environment management tools rather than depend on Group Policy Preferences in an attempt to get logon times quick. When you do not have a large amount (or any) CSE's in the use context, the difference between **Synchronous** and **Asynchronous** is not all that big. The two decent size projects I delivered at the time both used 2.9.7349.30108 and both used GPP heavily.

Working on a new engagement, the customer (hello) wanted to retain GPP for simplicity, this has proven an issue as we now have a significant logon tax that we do not want, off to WEM we go, which is great, but a shame that it is a forced move.

You will be feeling this if any of the following are true:

-  You are on an FSLogix agent version newer than 2.9.7349.30108
-  You use client-side extensions (CSE) (Group Policy Preferences, Folder Redirection etc)
-  You sold the dream of FSLogix "speeding up logons" (I won't get started on that)

You can reduce the impact by:

-  Reducing CSE usage
-  Migrating existing GPP and CSE to an environment management tool of your choice
-  Using an old version of FSLogix apps (2.9.7349.30108) (not a good idea)
-  Asking Microsoft if we can have this functionality back

## Summary (for now....)

I feel like there is more to this story. Microsoft surely made this change for technical reasons and due to some problem somewhere experienced by someone, but I know for sure that the customers I deployed to, had no issues with Asynchronous processing (see update above), so I am not sure why the global change, and how this isn’t an optional setting.

Surely there is more to be uncovered here, and I am hoping there is a way to bring back the good days.

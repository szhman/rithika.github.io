---
layout: post
title: "Profile Management in 2019: What? How? Why?"
permalink: "/profile-management-in-2019-what-how-why/"
description: "Discussing some profile management options and considerations for EUC platforms"
categories: [Citrix, FSLogix, Profiles, UPM, WEM, Windows, XenApp]
redirect_from: 
    - /2019/06/12/profile-management-in-2019-what-how-why
    - /2019/06/12/profile-management-in-2019-what-how-why/
image:
  path: /assets/img/profile-management-in-2019-what-how-why/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

EUC and User Profiles. Everyone's favourite topic and one that can raise some heated discussions on why, how, who, when and what should I do to manage these things, ideally with as little administrative sleepless nights as possible. Profile Management has not typically been the friend of your every day fun loving EUC admin.

The last few years has been interesting in this space, with technologies and techniques popping up all over the place trying to ease the pain. The rapid releases of Windows 10 in particular have managed to create that much noise around profile management that the world is full of opinions and views on how to deal with the chaos in the EUC space. So here is my take on it.

## The Basics. What is a user profile and is it that important?

A user profile is effectively a collection of settings and configurations which define how the user operates and interacts with their desktop environment. Some of this is customisable and set by the user (backgrounds, taskbars, desktop icons and layouts) and some of this is set by IT departments by using a myriad of tools such as Group Policy (GPO), User environment management solutions (UEM), Scripts etc. A profile more often than not, contains application settings and configurations specific to that user.

Sadly, a profile is typically tied to an operating system version, a Windows 7 profile doesn't work with Windows 8, nor does a Windows 8.1 profile work with Windows 10. Hell, half the time Windows profiles don't work between Windows 10 versions and they sure don't work between desktop and Server OS which isn't much fun at all.

## What sort of profiles exist, and why do we use them?

### Local (Glorious)

A local profile is the default happy place in any windows environment. No profile management, no worries. The profile lives locally, it contains by default, absolutely everything associated with that users operating environment. You upgrade windows, you upgrade your profile…too easy really. That is of course, until you sign in to another machine and start all over again. Uh Oh.

User experience fail number 1.
{:.user_experience_fail}

### Roaming Profiles

Microsoft Introduced roaming profiles way back in the day, the pretence being: *how about we take the local profile, move it to a network location, and then roam it around multiple machines*.

Great plan, except it often sucked. I know I know, we all start somewhere, but give me anyone who enjoyed working with Microsoft roaming profiles and I'll eat my own face.

Roaming profiles grew legs and it became commonplace. They filled a gap that we as admins and users as users needed filled, and whilst it solved some challenges, the user experience was often slow and painful, it hurt admins sleep patterns, and it was not an enjoyable time to live in the EUC world (yet it's still out there). I have seen miracles performed by admins with Roaming Profiles, the patience they have to tweak these things to within an inch of their life is nothing short of Medal worthy, but reality is, this is often due to implementations that try to address situations that simply should not exist.

User experience fail number 2.
{:.user_experience_fail}

Microsoft did admittedly have some neat tricks up their sleeves with mandatory profiles and even super mandatory profiles if you really needed to lock and control your environment, but other solutions also provided the same (read on).

The resting place of Roaming Profiles:
[![Rubbish]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/Rubbish.png)]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/Rubbish.png)

My friend and the industries go to guy for anything profiles based [James Rankin](http://james-rankin.com/) has an upcoming post on the history of profiles, he will as always no doubt get into the good stuff at a far deeper level than I am here, watch out for it.

> (Updated - James' Post is [live](https://james-rankin.com/features/the-history-of-the-windows-user-profile-in-euc-environments-1994-2019/) and well worth the read)

### Citrix Profile Management

More on this a little later, but Citrix introduced a technology (via sepagoPROFILE, thanks [Helge](https://helgeklein.com))) that offered a far better option for profile roaming in the Citrix world: [Citrix User Profile management](https://www.citrix.com/en-au/go/jmp/upm.html) (UPM).

[![UPM]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/UPM.png)]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/UPM.png)

It was light, it was flexible and for the good part of 10 years, has been the defacto standard for Citrix environments. Unfortunately, UPM was still a solution that suffered from many of the challenges of Microsoft Profiles, albeit with a load more mitigation techniques and logic to help protect admins and users from a world of pain.

One of the things I *really* liked about UPM was how seamlessly it could migrate a Microsoft Roaming Profile into a Citrix UPM Profile, I recall clearly one project I ran where I had around 5K Microsoft Profiles to move across to Citrix UPM. Not a single support call. Not one! The process was seamless, the performance increase was obvious and the profiles simply moved across without an issue.

Not perfect, but a load better than Roaming Profiles in a Citrix environment. Let me digress for a short while onto the companion of profile management in folder redirection.

### Folder Redirection

Folder Redirection combined with Profile Management, be it Microsoft, Citrix or any other player really opened up a few doors and architectures that somewhat helped (at a cost) our scope of profile management.

Folder redirection let us take critical user components out of the profile and put it on a network share, Documents, Desktop, Start Menu (before we went modern) and all sorts of user important data which really helped make the user "profile" less critical. Now we can access user data from any device (given appropriate connectivity), from any OS version at pretty much any time, providing a level of consistency and familiarity simply not possible with a standard profile management solution.

Folder redirection is [not without its drawbacks](http://james-rankin.com/articles/citrix-xenapp-xendesktop-and-folder-redirection-the-last-word/), however I still firmly believe it has a place in enterprise, James Rankin has an interesting read [here](http://james-rankin.com/articles/citrix-xenapp-xendesktop-and-folder-redirection-the-last-word/) if you want to understand some of the finer fun points and some methodologies that I tend to agree with for the most part.

[![FolderRedir]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/FolderRedir.png)]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/FolderRedir.png)

A common bearer of misery and endless sorrow often utilised to plug some holes in folder redirection, is the magic that is Offline Files.What a glorious, file corrupting, data isolating, user enraging system this was. Offline Files was a great concept, but a [horrible reality](https://helgeklein.com/blog/2012/04/windows-7-offline-files-survival-guide/) that resulted in next to no happy stories.

User experience fail number 3.
{:.user_experience_fail}

You have all been there. I won't waste any more of your time in going further down that path in life again, needless to say, it's not right and thank god basic concepts such as OneDrive and Known Folder Move address some of the utter rubbish we had to manage back in the day.

So, with our basic folder redirection in place, and assuming that we ignore the whole hysteria inducing deployments of offline files, it seems like job done really. Now we can blow away a profile, and all we lose is junk data right? Wrong. Three Words: **Application**. **Bloody**. **Data**.

AppData is where most of our applications store their data, some in local (typically throw away), some in roaming (we need this to follow us around so that our apps look, feel and operate the same). The problem here becomes obvious, the solution even more so based on above. Just redirect AppData too! Bup. Bow.

This sucked. AppData redirection is categorically horrible. It's a performance nightmare, it can lead to all sorts of weird and wonderful app behaviours and it's something that should simply die. Want to have a good fun read, churn through this series by [Aaron, Shawn and Helge](https://stealthpuppy.com/folder-redirection-2015-part-1/)

What got worse again is that many applications are now being written to live in the user AppData exclusively. Slack, Teams, WhatsApp, Github Desktop… anything based on the [Electron framework](https://electronjs.org/apps) really. This framework kills profile management.

If we want AppData redirection to die, then what are our options? We need apps to work else it's a big User Experience Fail

### Citrix UPM (continued)

Microsoft and Citrix UPM both have selective ways of dealing with AppData. Microsoft Profiles are dead to me, so I am not going to focus on them. Citrix UPM on the other hand is very much alive and until recently, has been my profile management flavour of choice.

Citrix User Profile Management allows us to fine grain every component of the profile, we can include, exclude, synchronise and mirror profile components and we can pretty much achieve everything with UPM that you need in an EUC environment. We can stream, we can containerise small portions, we can enable active write back (at your own risk and only if specifically needed), we can really get pretty funky and to date, there hasn't been much that can't be done, albeit with bugs and challenges along the way, particularly whilst trying to keep up with the windows 10 release cadence.

There are three methods here when dealing with AppData via Citrix UPM:

-  Include everything. This means your profiles are huge and will bog down and start to cause performance issues.

User experience fail number 4
{:.user_experience_fail}

-  Include what you need and ignore everything else. This is a Nirvana state really and not something I have seen done well with UPM unless very much fine tuned. Other profile solutions chose to implement this model as the default, Citrix recommended it as the best path, but the overhead of management was huge
-  Exclude a whole load of useless crap that you know about and include everything else. This is realistically the most common way of handling UPM. Citrix give you templates, the community gives you better templates (thanks [James Rankin](https://www.htguk.com/everything-you-wanted-to-know-about_23/)) and it tends to be the path of least resistance. Its biggest challenge is when new apps onboard, and they do stupid things (I am looking at you Slack, Teams) and store themselves in the user profile. Then we end up having to back track on how and what to exclude

UPM also offered us the capability to define a mandatory profile, which basically refused to let users customise their environment, and forced a set *template* profile. This was highly efficient and streamlined resulting in pretty damn good performance, however not always fit for the majority of use cases and at times a bit of a nightmare to manage.

### Containers via UPD

The world changed when virtual disk formats landed. It got even more intriguing when we started looking at containerising profiles.

[Microsoft User Profile Disks (UPD)](https://techcommunity.microsoft.com/t5/Enterprise-Mobility-Security/Easier-User-Data-Management-with-User-Profile-Disks-in-Windows/ba-p/247555) was the hidden gem in RDS deployments. It was limited, it was full of caveats, but in the right situation it was rock solid. The basic pretence: Take a local profile and stick it in a container (VHD/VHDX). Mount the container when the user logs on, present the profile in way that the operating system doesn't care about, and bobs your uncle - all the good things we spoke about in local profiles, extended out to effectively a "roaming" profile.

This was really cool, but primarily only available to RDS environments, Windows Server 2012 R2 onwards and was barely supported outside of this model (yes, I know it worked, but it wasn't production *OK* to deploy it unsupported). Conversely, there was some interesting reading available around how and when this might be supported, as well as how it technical can be used in [certain scenarios.](https://4sysops.com/archives/user-profile-disks-on-windows-10/)

### Citrix App Layering User Layers

Citrix App Layering provides a [container based user layer](https://docs.citrix.com/en-us/citrix-app-layering/4/layer/enable-user-layers.html) which by default will consume the user profile and persist it. This feature is only new and out of labs recently, and to be honest, the overhead of having to deploy an entire App Layering infrastructure to consume it rules it out for me. The concept is great, but lets talk again when the deployment is simplified, stabilised, and offers the premium features available with something a little more mature in the market.

[![User Layers]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/UserLayers.png)]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/UserLayers.png)

### Enter the grand Container master. FSLogix

This really is the *almost* perfect profile solution for non persistent environments. It had all the benefits of a local profile, and all the awesomeness of a container. Ease of management, reduced IO, consistent user experience, the list keeps going. FSLogix took what UPD had started, created a monster of a solution with loads of bells and whistles which really exploded in the industry. The key to it all: *Simplicity*.

[![FSLogix]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/FSLogix.png)]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/FSLogix.png)

A couple of points I want to talk to around FSLogix Containers:

-  This solution is so damn good, Microsoft [bought them.](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/) My money had always been on Citrix acquiring them, but Microsoft doing so was a gigantic statement
-  Containerisation of a profile is not the same as roaming a profile. Containers provide you with local profile `portability` rather than profile `management`. Why is this important? Because the entire premise of this solution is that you *don't* `manage` a profile. You don't configure it, you don't exclude data from it (there are exceptions to this), you don't tweak it. You simply don't care (stay with me here…). What you have on a local Windows machine, you have on many local windows machines by way of a container attach
-  It is not a solution to speed up your logons. Container attach is not necessarily faster than a Citrix Profile for example. With the right love, a Citrix UPM profile will stream, and load faster than a container can mount. But that's ok, the weigh off here is that we have a guaranteed identical user experience across many machines without spending hours, days, weeks of time to streamline profile management, and dealing with changes over and over
-  FSLogix supports multi session access via a combination of differencing disks and session disks depending on the use case. I wrote a piece previously about [how this fits together](https://jkindon.com/2018/10/22/addressing-multi-session-profile-management-with-fslogix-containers/), one of many features that sets FSLogix apart from others who provide container technology.
-  Many organisations are going to be entitled to this solution under the new licence offerings, there is really no reason not to consider it should you match any of the licencing entitlements being offered by Microsoft since the acquisition

This theory is awesome, until you start realising that the cost of this simplicity is typically storage - what you don't care about on a local machine with a big hard drive, you suddenly care a lot about when the same data lives on your premium arrays. There are techniques to manage this, led mostly by [Aaron Parker and his pruning scripts](https://stealthpuppy.com/fslogix-containers-capacity/), but lets be clear - this is about capacity management, NOT profile management.

There are a squillion different articles on how to setup manage and utilise FSLogix Containers, a quick google search will put you in the right direction, however one of my favourite reads and references is [Leee Jeffries Sizing post](https://www.leeejeffries.com/my-experiences-sizing-fslogix-profile-and-o365-containers/). This is a great read when you consider the impact of profiles on your environment when containerised.

How to get your existing profile solution into FSLogix Containers? This is a bit of an art, there are a few options:

-  [Citrix UPM to FSLogix Container](https://blog.fslogix.com/citrix-upm-profiles-to-fslogix-profiles-conversion-powershell-script) - Community based via [David Ott](https://blog.fslogix.com/author/david-ott)
-  FSLogix also had a migration tool, the detail has been removed from the site, but a quick google cache search bring its back to life [here](https://webcache.googleusercontent.com/search?q=cache:Kt4_nRsvpAMJ:https://docs.fslogix.com/display/FA28/Profile%2BMigration%2BGuide+&cd=1&hl=en&ct=clnk&gl=au)
-  [Local to FSLogix Container](https://docs.fslogix.com/pages/viewpage.action?pageId=6553650) - frx.exe using the copy-profile switch or [an iteration of the above Script](http://www.citrixirc.com/?p=857) added by Ryan Gallier
-  UPD to FSLogix – Pending, haven't seen this done yet, however [Rene Bigler](https://twitter.com/dready73) pointed me to [here](https://www.beckmann.ch/blog/2019/05/17/migrate-user-profile-disk-to-fslogix-profile-disk/?lang=en) for a project he worked on

### The others

There are other players in the market, but I don't deal with them, so it's not that I am ignoring them, it's just not something I am familiar with. David Wilkinson has a really good read on [Office 365 Data Persistency](https://wilkyit.com/2018/01/19/office-365-in-non-persistent-environment-product-comparison-matrix/) which compared some products on the market, these all tend to have profile management solution attached to them as well

## And User Environment Management?

User environment management (UEM) is a key component of making a profile less critical. Whilst a profile contains user personalisation and the general operational state of the user, it should still realistically be something that can be thrown away and recreated should the need arise. This is where UEM comes into play.

Be it Group Policy, Scripts, Ivanti, Citrix WEM, Tricerat or any other user environment solution, the general concept is to build out the critical parts of the user profile on the fly - Drives, Printers, Settings, Configurations, Environmental Variables, DSN's, Start Menu's, Desktops, Taskbars etc.

Resetting a user profile should be no more than a small hindrance to the user, resulting in maybe setting up some customisations that make their life easier and allow them to work in the way that suits them, it should never ever result in data loss or application failure due to lost configuration. If it does, you should be moving that component into some sort of UEM delivery.

This is the ideal state, not always achievable in all cases, but many UEM solutions offer ways to make this a reality that is closer than you think, what's better, is that most Citrix customers are entitled to Citrix WEM, and Microsoft GPP whilst not optimal, is also a great option to get you a step closer.

[![HowitFits]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/HowitFits.png)]({{site.baseurl}}/assets/img/profile-management-in-2019-what-how-why/HowitFits.png)

## Which Profile Goes Where?

Given my obvious enjoyment of containers, is it now the default standard for every environment and every use case? Is every other form of profile management particularly in my day to day; Citrix UPM dead? No. I don't think so.

There are times when FSLogix Containers is the default answer. Published Desktops, Virtual Desktops, or even physical desktops in some scenarios where my users are operating in a full desktop experience environment, I am using a container unless I cannot. The simplicity and guarantee of all things working simply makes it a no brainer.

But what about published specific applications that serve one purpose and do not need to interact with anything else. Do I need a container which effectively owns the entire user profile and the overhead of storage that goes with it? No. Citrix UPM from a profile management perspective is far finer grained. I can drill down and make it include one simple folder, resulting in lightning fast logons and the customisation I need. No point in bringing the entire user experience if I need one or two folders to provide my application settings in a portable fashion.

There are differencing views on this point, there are also solutions to make a container do the same thing, but there is a tax to achieving this with a container which does not exist with UPM for example. Remember, containers are designed to be simple, profile management is not the primary focus of these solutions.

## Summary

To summarise, most of this is opinion based on my own experience however should most probably align with what many of you are familiar with. If I can leave you with anything it's this:

-  Profile Management is a fine art. If there is business critical data or configuration in a user profile that could be lost on a reset, then it should be moved to a UEM delivery
-  Managing profiles is not always best suited to a single solution. Whilst Containers are the superior and future of Profile Control, they don't 100% negate the use case for other solutions
-  Profile Containers are typically about local profile portability. The idea of them is to simply bring the benefits of a local profile to a non-persistent environment without having to "manage" them. Any tweaking or management that you do, will be about capacity management, not application compatibility. Choose a method for this wisely and seek community advice/findings
-  Profile Containers are not used to speed up logons. This is a common misconception and should not be a driving factor for containers. A finely tuned profile management solution will typically be faster to load than a VHD mount and file system rewrite

I hope this was as fun to read as it was to write….Until the next rant…

---
layout: post
title: "File Type Association with WEM and SetUserFTA"
permalink: "/file-type-association-with-wem-and-setuserfta/"
description: "Conquering nasty file type association challenges with SetUserFTA"
categories: [Citrix, FTA, WEM]
redirect_from: 
    - /2018/01/02/file-type-association-with-wem-and-setuserfta
    - /2018/01/02/file-type-association-with-wem-and-setuserfta/
image:
  path: /assets/img/file-type-association-with-wem-and-setuserfta/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

I am sure many of you have had the enjoyable experience of dealing with user FTA (File Type Associations) within Windows 10 and Server 2016 (Windows 8 onward is a jerk really..)

For those that aren't aware, Microsoft made a nice little change for us which made FTA assignment on a per user basis almost impossible without loads of hacks and cracks. James Rankin has a good write up [here](https://www.htguk.com/deploying-per-user-file-type/). Sure, there are ways to pre-populate the FTA's and app associations etc, but its stored in a computer context not user, and just isn't a solution that addresses the required flexibility that most organisations with SBC environments have.

Luckily, there are some clever cookies amongst the ranks of those that don't like to be told "no", such as one Mr [Christoph Kolbicz](https://twitter.com/_kolbicz). He has managed to successfully reverse Microsoft's decisions to block our ability to control this nicely, and has written a cracker utility called *SetUserFTA*. You can find his blog, his explanation and his tool (which he offers to the community) [here](http://kolbi.cz/blog/?p=346).

WEM has great ability to control FTA natively, however with the latest releases of Windows 10 and Server 2016, I am finding that the behaviour is not as smooth as it once was with older Operating Systems, and settings either aren't honoured by Windows, or result in far too many prompts for the user.

I find the easiest way to drive this magical new utility, is of course however, via WEM and there are two options for control here.

**Option1.** Create an external Task in WEM, point it to the SetUserFTA.exe and assign it to all users. *SetUserFTA* allows you to specify a configuration file, which includes the ability to specify FTA settings per Active Directory group. This, from a WEM perspective means simply launching the SetUserFTA.exe and passing the assignment component back to the Config File.

[![Task1_FTA]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Task1_FTA.png)]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Task1_FTA.png)

And the config file:

[![FTA_Config1]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/FTA_Config1.png)]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/FTA_Config1.png)

**Option 2** (my preferred) is to bring the visibility back into WEM, utilise multiple external tasks, and assign to your users based on your normal WEM processes. This option keeps visibility within WEM around your assignments for those that like single pane of glass and don't like the black hole of multiple configuration points in the environment.

[![Task2_FTA]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Task2_FTA.png)]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Task2_FTA.png)

It's important to note that for some applications, you will need to create the associated `HKCU\Classes` key for the application. This of course, can also be actioned with WEM by creating a registry action (Thanks Munro for the nudge)

[![Reg_FTA]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Reg_FTA.png)]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Reg_FTA.png)

Standard WEM assignment process for User 1:

[![Assignment_FTA]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Assignment_FTA.png)]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Assignment_FTA.png)

And a second assignment for User 2:

[![Assignment2_FTA]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Assignment2_FTA.png)]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Assignment2_FTA.png)

Below you can see the results in action. This is the same server with the 2 different user sessions active Left hand side has User2's association set, and right hand side has user 1's association set:

[![Results]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Results.png)]({{site.baseurl}}/assets/img/file-type-association-with-wem-and-setuserfta/Results.png)

And what happens if you accidentally add someone to two methods of association?…last write wins :)

Hats off to you Mr Kolbicz, well played.

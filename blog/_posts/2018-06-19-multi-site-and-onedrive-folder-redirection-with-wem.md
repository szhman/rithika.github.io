---
layout: post
title: "Multi Site and OneDrive Folder Redirection with WEM"
permalink: "/multi-site-and-onedrive-folder-redirection-with-wem/"
description: "Advanced multi-site single config set configurations for folder redirection with WEM"
categories: [Citrix, UPM, WEM, Windows]
redirect_from: 
    - /2018/06/19/multi-site-and-onedrive-folder-redirection-with-wem
    - /2018/06/19/multi-site-and-onedrive-folder-redirection-with-wem/
image:
  path: /assets/img/multi-site-and-onedrive-folder-redirection-with-wem/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

A more common theme and demand arising within the WEM space now that its gained such a large amount of traction, is the ability to handle multi-site or multi-region Folder Redirection (USV) and Profile locations within native WEM configuration. At first glance this can appear to be more complex than it actually is, so I spent some time documenting some basic guidance using a completely "Within-WEM" configuration. A couple of key reasons that I had typically not utilised WEM for folder redirection on many projects were:

1.  The lack of native flexibility and easy division of redirection requirements, and
2.  Some nasty inconsistencies in applying the configurations, of which simply do not seem to exist within GPO (more on this later – I have resolved this challenge)

The example that I have written up below focuses on Folder Redirection, the same principals apply to Profile Management however given how and when UPM settings are processed, your options are not as flexible, and you are best off reading through Carl's guidance [here](http://www.carlstalhood.com/citrix-profile-management/#multidatacenter), and applying it to your WEM policies.

To clarify this, Folder Redirection is processed in the user context post logon via WEM, UPM Processing happens as part of the logon, to get your profile in and going. Folder Redirection can thus utilise environmental variables processed and set by WEM To set the scene, I have the following setup and basic requirements in my lab scenario

-  I have 4 sites with 4 user sets. Sydney, Brisbane, Melbourne and Perth
-  Each site has its own File Server used to locally house users Redirected Folders. My environment sits behind a single DFS Name Space: `Kindo.com`
-  I have a single WEM Configuration Set as my XenApp Workers are all the same specification and perform the same role regardless of their location
-  I am undertaking a migration to OneDrive project, and as part of this deployment, I would like my redirected folders to be housed in OneDrive. Users could live in any site and the rollout is staggered
-  I currently have an OU structure that geographically segments my users, however this might change so I need the solution to be adaptable moving forward

Typically, if we were driving folder redirection via GPO, the solution is simple – a policy per OU and away we go, however with WEM there is a few more touch points, which ultimately provides you with more flexibility

## DFS-Namespace

As I mentioned above, I have an existing DFS Namespace that covers my organisation. I am not getting into the Replication debate here, simply DFS-N configured as a single access namespace. Assume that each folder is targeted to an individual server.

[![DFS-N]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/DFS-N.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/DFS-N.png)

Truth be told, its currently pointing to two as I am not nerdy enough to have **4** file servers in my lab. As such, Server 1 houses Sydney and Melbourne Data. Server 2 houses Perth and Brisbane Data. But you get the point

## WEM Environmental Variables

This is not rocket science what we are trying to do here, but it's important to understand how and why this is possible within WEM.

WEM processing is split into **3** parts as I have touched on a few times in different posts [WEM Advanced Guidance](https://www.mycugc.org/blog/wem-advanced-guidance---part-1)

1.  WEM Service at Machine Boot – processes machine-based settings (inc UPM configuration) and Optimizations
2.  WEM Service at User Logon – processes environmental settings, AppLocker Policies and System Utilities
3.  WEM User Agent – Processes the user environment **post** the WEM Service finalising its work. This is where folder redirection occurs. Importantly here, Environmental Variables are applied before Folder Redirection occurs, which means they are the perfect tool to manipulate pathing. WEM is also smart enough to handle expansion of these variables, so we are good to go

In our scenario above we have **5** variables we need to create. Each one of these will basically set the location of where we would like the user's data to land.

I have called the Variable `%FolderRedirLocation%` you can call it whatever you like... Mary, Barry, Steve, Fred... whatever suits.

I have created **5** objects within WEM, creating **5** variables with the same name. Confused? Read on.

| **Variable** | **Path** |
| :-- | :-- |
| %FolderRedirLocation% | `\\Kindo.com\Profiles\Sydney\` |
| %FolderRedirLocation% | `\\Kindo.com\Profiles\Melbourne\` |
| %FolderRedirLocation% | `\\Kindo.com\Profiles\Perth\` |
| %FolderRedirLocation% | `\\Kindo.com\Profiles\Brisbane\` |
| %FolderRedirLocation% | `##UserProfile##\OneDrive – Computer Systems Australia Pty Ltd` |

[![Variables1]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Variables1.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Variables1.png)

Each of the on premises site-based variables are configured as follows:

[![VariableConfig1]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/VariableConfig1.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/VariableConfig1.png)

The execution order for these is left at the default **0**. I am going to be filtering them shortly, so I don't care what order they are executed in

[![VariableConfig2]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/VariableConfig2.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/VariableConfig2.png)

The OneDrive Variable is configured a little differently, I am actually setting the location of the variable to the local Sync Client found on the local machine. In my example below, my corporate OneDrive: `%UserProfile%\OneDrive – Computer Systems Australia Pty Ltd`

[![VariableConfig3]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/VariableConfig3.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/VariableConfig3.png)

Note here though that I change the execution order to **1** as I want this to process AFTER my initial variables do. I will have some logic to check things added shortly

[![VariableConfig4]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/VariableConfig4.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/VariableConfig4.png)

## WEM USV (Folder Redirection Configuration)

I think its best at this point to understand that what we have done is built some variables, nothing else. They aren't assigned, they are simply objects. If we now switch over to the WEM USV Configuration, it's easy to keep a track of how this fits together.

My configuration paths have been aligned to my Environment Variables. Note that you are probably all smarter than I am, but years of doing this with a `\\Server\Share` mentality caused me headaches when using Environmental Variables at the root path. Windows expands these natively, so **%FolderRedirPath%** = `\\Examplelocation` whereas `\\%FolderRedirPath%` is nothing of use

[![USV1]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/USV1.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/USV1.png)

USV Options 1/2
{:.figcaption}

[![USV2]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/USV2.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/USV2.png)

USV options 2/2
{:.figcaption}

Note that I purposely mixed and matched the variable use above with a combination of Native WEM and Native Windows variables. This is to prove there is no set way to do it, and you can choose whatever makes you happy.

The result here, is that once the variables are correctly set, the pathing should expand to something like below:

| **User** | **Path** |
| :-- | :-- |
| Sydney User | `\\Kindon.com\Profiles\Sydney\S_User1\Documents` |
| Melbourne user | `\\Kindon.com\Profiles\Melbourne\M_User1\Documents` |
| OneDrive User | `C:\Users\JamesK\OneDrive – Computer Systems Australia\JamesK\Documents` |

You get the drift

## Conditionally Assigning the Environmental Variables

WEM is ridiculously awesome as far as fine-grained filtering and conditional assignments, in this example I am going to do a very basic configuration, targeting an OU location, however the world is your Oyster as far as fine graining this stuff.. You can add location attributes in AD, you can do client name detections, address ranges etc. You can make this is as simple or as complex as you need to with multiple conditions.

As you can see below, I have a very basic structure setup in Active Directory, breaking my users up regionally.

[![AD1]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/AD1.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/AD1.png)

I also have a single group configured for WEM assignment purposes, you don't need to do this, you can use any of your existing Groups

[![AD2]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/AD2.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/AD2.png)

Within WEM, Under **filters -> conditions** I have created **5** new Conditions

[![Conditions1]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Conditions1.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Conditions1.png)

**4** of these conditions are configured to detect my users OU location `Active Directory Path Match`

[![Conditions2]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Conditions2.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Conditions2.png)

Don't forget to add the "`*`" at the end of an OU match else it will fail
{:.warning}

> OU=Sydney,OU=KindonEnterprises,DC=Kindo,DC=com

My fifth condition for OneDrive is different, it is looking for an Environment Variable that OneDrive itself creates once configured. In this case I am using the `Environment Variable Match` Criteria and looking for the value: `##UserProfile##\OneDrive – Computer Systems Australia Pty Ltd`

[![Variable1]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Variable1.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Variable1.png)

OneDrive Environment Variable
{:.figcaption}

[![Conditions3]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Conditions3.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Conditions3.png)

OneDrive Filter Condition - Variable Match
{:.figcaption}

Now that I have some conditions, I need some rules. Rules are used when `assigning actions` and `contain` one or many `conditions` with an `and` logic

Under **Filters -> Rules** I have created **5** new rules

[![Rules1]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Rules1.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Rules1.png)

Four of these rules will be used for my on premises users and are configured as below. Note that each rule should be named in an easy to read fashion for when you carry your assignments, conversely, I find it helpful to name my conditions very descriptively for the same reasons

Each `rule` contains the relevant `condition` (The `Rule` for Sydney contains Sydney's `Condition` and the `Rule` for Melbourne contains Melbourne's `Condition`)

[![Rules2]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Rules2.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Rules2.png)

My OneDrive `rule` contains my OneDrive detection `condition`

[![Rules3]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Rules3.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Rules3.png)

Now that everything is configured, we simply `assign` our `Variables` to the control group of choice, and `filter` each `assignment` with a specific `Rule`

[![Assignment1]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Assignment1.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Assignment1.png)

## Mapping the process out

Now when the users sign in, the process is as follows.

WEM Checks for `assignments` and processes the `environmental variables`, setting it for the user.

The example below shows a user with both a **Sydney** and **OneDrive** `Environment Variable` set.

[![Variable2]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Variable2.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Variable2.png)

Note that due to the execution order, my user who is **Sydney** based AND has **OneDrive**, now maps to **OneDrive** based on his `Environmental Variable`.

In the next example, I have disabled the **OneDrive** `Variable` check, effectively simulating the same user without **OneDrive** in the mix.

[![Variable3]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Variable3.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/Variable3.png)

Note that he is now just an everyday **Sydney** user WEM now processes the `folder redirection` for the user based on the expanded `variable` path

[![USV3]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/USV3.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/USV3.png)

## Summary and Things to Note

For a long time now I have been perplexed about some of the other behaviours associated with Folder Redirection and WEM, to the point that I simply don't include it in most of my deployments, and leave things GPO driven. My biggest challenge was the refusal of WEM to ever self-heal folder redirection if for some reason the redirected folders were altered, or there was a problem of some sort that required manual interaction with the redirected folders.

Update: this is not just limited to WEM it would seem, but is a native issue with GPO driven Folder Redirection also.
{:.warning}

There were two actions that would resolve the issue

1.  Remove the users profile entirely and start again – this led me to be thinking that the tracking of WEM actions within the user profile was the root cause
2.  Change the variables on the actual folder paths, this seemed to trigger off a "reprocess" of changed values – again pointing to tracking

Digging in to the support article [here](https://support.citrix.com/article/CTX219086) it is noted that you can delete the entire `HKEY_CURRENT_USER\Software\VirtuAll Solutions\VirtuAll User Environment Manager\Agent\` Key which effectively removes all of the users cached actions for WEM, and the tracking of what's been applied at the user level.

Digging in a little deeper, we can see there is a specific key which relates to the processing of folder redirection, this specifically is the `UsvUserConfigurationSettings` Key, with a sub key identifying the user via its `GUID`.

[![RegKey1]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/RegKey1.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/RegKey1.png)

HKEY_CURRENT_USER\Software\VirtuAll Solutions\VirtuAll User Environment Manager\Agent\Tasks Exec Cache\UsvUserConfigurationSettings
{:.figcaption}

[![RegKey2]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/RegKey2.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/RegKey2.png)

UsvUserConfigurationSettings Values
{:.figcaption}

As such, rather than kicking the entire cache key itself, simply removing the **GUID** Sub key below forces WEM to reprocess USV settings, and we have our folder redirection self-healing back in action. Problem solved.

[![RegKey3]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/RegKey3.png)]({{site.baseurl}}/assets/img/multi-site-and-onedrive-folder-redirection-with-wem/RegKey3.png)

I would suggest that this is a little flaw in WEM processing, and that it should be checked and processed on every pass – maybe there is an option somewhere for this hidden away, I am not sure.

I also want to make it very clear that this solution in regards to OneDrive is going to need to have the above performed at time of change. Given that WEM lacks the constant reprocess at the moment, if you switch locations, you are going to need to force it to reprocess again (Update: this does not appear to be a challenge from 1903 onward - changing the paths is honored and reprocessed). It will also not move the data, you could probably work out some logic within WEM itself to handle this with file copies etc – but that's a post for another time.

Anyway, hopefully this can help some of you battling with the not so obvious (but extremely capable) settings required to address multi location requirements in a single configuration set scenario within WEM

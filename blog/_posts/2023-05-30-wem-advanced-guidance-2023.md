---
layout: post
title: WEM Advanced Guidance 2023
permalink: "/wem-advanced-guidance-2023/"
categories: [Citrix, WEM, Windows, CVAD]
description: WEM Advanced Guidance 2023 - 5 years on
image:
  path: /assets/img/wem-advanced-guidance-2023/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
related_posts:
  - blog/_posts/2017-11-30-wem-advanced-guidance-part-1.md
  - blog/_posts/2018-01-04-wem-advanced-guidance-part-2-user-interaction.md
  - blog/_posts/2018-02-08-wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly.md
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Setting the Scene

This is a [post](https://blogs.mycugc.org/2023/05/30/wem-advanced-guidance-2023/) which I initially wrote for CUGC, re-posted here.

Way back in 2017, Hal and I sat down over a few months and wrote up a series for CUGC based on Citrix Workspace Environment Management. We badged the 3-piece series of articles as "WEM Advanced Guidance", the aim of them was to shed some light on all the ins and outs of WEM and roll through some fundamentals of the solution. You can find the original articles below:

-  [WEM Advanced Guidance – Part 1](https://jkindon.com/wem-advanced-guidance-part-1)
-  [WEM Advanced Guidance – Part 2: User Interaction](https://jkindon.com/wem-advanced-guidance-part-2-user-interaction)
-  [WEM Advanced Guidance – Part 3: The leftovers, Good, Bad, Ugly](https://jkindon.com/wem-advanced-guidance-part-3-the-leftovers-good-bad-ugly)

That series, believe it or not, wrapped up in February 2018 which means it's been over 5 years. That means it's most definitely time for a refreshed WEM Advanced Guidance 2023, because the solution has not sat still, and there are a lot of changes to discuss, including updated guidance and changed logic. Let's get cracking.

## WEM Architecture and Deployment Options

Back in the old days, there was one client-server architecture for WEM which required a database server, an application broker(s) a load balancer and several agents. Whilst this model still exists, we also now have a Cloud version of WEM delivered "As a Service" as part of the Citrix Cloud DaaS offering.

When deploying [on-premises](https://docs.citrix.com/en-us/workspace-environment-management/current-release.html), the following architecture applies:

[![WEM On-Prem]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-2023.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-2023.png)

When deploying the [cloud service](https://docs.citrix.com/en-us/workspace-environment-management/service.html), things change up a little:

[![WEM Cloud]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-cloud.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-cloud.png)

The service has a considerably faster release cadence, additional features, and a more enjoyable administration experience via the web console. Amazingly, despite the [documentation](https://docs.citrix.com/en-us/workspace-environment-management/service/system-requirements.html#connectivity-prerequisites) there still seems to be a significant amount of forum posts and customer challenges associated with WEM Agent communications to the cloud. To summarize how things work with the cloud service:

-  The Cloud Connectors are there to proxy the agent registration with the WEM service and to allow the WEM service to understand Active Directory.
-  The Agents themselves must have 443 outbound access to the WEM Services. They do not proxy to the cloud service via the Cloud Connectors. Never have.

The service offered means that Citrix managed your database (Azure SQL), the Broker Roles, and the Console, both Web (modern) and full (legacy). You simply manage Cloud Connectors and Agents.

## Configuration Sets

Things change a little with configuration sets. The ability to now have dynamic optimization settings negated that as a requirement to split out configuration sets. VMware persona features being deprecated and then removed also negated that as a segmentation requirement.

Citrix also addressed the ability to synchronize settings across multiple configuration sets, meaning that you can now have full export and import capability including AD group assignments. This was a major win for multi-config set scenarios.

## Cache, Cache, Cache

Still the number one misunderstood function of WEM, the cache is getting (yet another) special mention. This is the single biggest challenge I see in the field.

There are three cache points:

-  The broker stores a Cache of the Database so that the SQL server doesn't get pounded and a level of resiliency for SQL outages is in place. This is the default behavior of WEM and a cache exists on each broker server.
-  Cache number two is the cache that exists on the Agent itself once offline mode is enabled

[![WEM Cache Settings]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-cache-settings.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-cache-settings.png)

  This is a constant point of misconfiguration. All deployments should use Offline Mode. This means that a cache will be created locally which provides both resiliency and faster processing by removing the load from the brokers. The "**use Cache to Accelerate Actions Processing**" drastically reduces load, as the Cache is used 100% to process the user actions, of which there are typically a lot. What often gets missed is that the cache is updated based on the **Agent Cache Refresh Delay** which is 30 minutes randomized so as to avoid load storms.

[![WEM Agent Refresh Settings]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-cache-agent-refresh.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-cache-agent-refresh.png)

You can read more on Cache via a [blog post released in 2020](https://www.citrix.com/blogs/2020/03/23/workspace-environment-management-agent-caching-explained/) by Wayne Liu.

-  The third cache is the user-based tracking cache. This cache lives in the user registry itself and tracks how, and when application processing occurs per user. This is the cache that tracks things like "**run once**" or "**Automatic Self Healing**" tasks. You can [read more about this cache](https://jkindon.com/selective-deletion-of-the-wem-actions-tracking-cache/) where I developed a quick PowerShell script to help reset these cache objects to help with re-processing actions. Luckily that script is no longer needed as the WEM team took the concept and built it into the product.

[![WEM Agent Reset Actions]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-reset-actions.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-reset-actions.png)

The cache world is not hard, but it's often misconfigured. [Here is a post](https://jkindon.com/the-why-does-it-of-citrix-wem-cache/) on how It worked historically before some code changes were introduced, but it is important to understand.

Additionally, the WEM documentation now outlines both the [Agent Startup behaviour](https://docs.citrix.com/en-us/workspace-environment-management/current-release/install-and-configure/agent-host.html#agent-startup-behaviors) and some detail around the [Agent Cache Utility](https://docs.citrix.com/en-us/workspace-environment-management/current-release/install-and-configure/agent-host.html#agent-cache-utility-options).

The biggest takeaway from all those articles is this:

"*When Citrix WEM Agent Host Service starts, it automatically verifies that the agent local cache has been recently updated. If the cache has not been updated for more than two configured cache synchronization time intervals, the cache is synchronized immediately. For example, suppose the default agent cache sync interval is 30 minutes. If the cache was not updated in the past 60 minutes, it is synchronized immediately after Citrix WEM Agent Host Service starts.*"

The final note on the cache side of things is this. I have done more deployments of WEM than I can count, and I have zero issues with Cache. The reason for this is twofold:

-  In legacy environments, before the code was updated to handle bad conditions, [I used a startup script](https://jkindon.com/citrix-wem-updated-start-up-scripts/)
-  Once I moved deployments (or implemented new ones) using the updated logic within the product, I removed the scripts and let the product do its thing. I do, however, deploy BIS-F in every single deployment I have done. With [BIS-F](https://eucweb.com/download-bis-f) I always enable the WEM processing as below

[![WEM BISF GPO]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-bisf-gpo.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-bisf-gpo.png)

If you are not using BIS-F in your environment, then go and slap yourself and then get it done.

If you screw up the way the cache works or don't configure the environment to allow it to work properly, you will not have a fun journey with WEM. Sort this and you sort 99% of issues.

## System Optimization

WEM offers a range of system optimization tools: CPU, Memory, I/O, Fast Logoff and Citrix Optimizer integration.

For **CPU Optimization**, way back in the old days, you typically had to tell WEM what sort of configurations it was going to use using a core/percentage-based formula. This meant you were limited to one size of machine per configuration set which was a bit rubbish. Luckily, this is now automatic, and you should be allowing WEM to dynamically figure this out using the **Auto Prevent CPU Spikes** setting.

[![WEM CPU Optimization]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-cpu-management.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-cpu-management.png)

WEM **Memory Management** is still the same as it was conceptually and is something to be wary of. At its core, Memory Management is forcing the paging of idle memory to disk. This is aggressive and can be extremely punishing on your disk configurations. In 99% of deployments I have done in the field, I do not enable Memory Management. If your VMs need more memory to handle the load and reach the density, then give them more memory, or deploy more VMs.

My colleague Dave Brett recently pushed some [Nutanix benchmarking on WEM impacts](https://portal.nutanix.com/page/documents/solutions/details?targetId=TN-2087-Performance-Considerations-Citrix-WEM:TN-2087-Performance-Considerations-Citrix-WEM) which is well worth a read and aligns with what I have experienced and configured in the field. Well worth a look as he did a great job with the write-up.

A new feature that was introduced into both cloud and on-prem deployments is **Memory Usage Limit** which lets you limit the memory usage of a process by setting an upper limit for the memory the process can consume.

[![WEM Memory Optimization]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-mem-management.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-mem-management.png)

Note, the above image is really a joke as far as processes defined – don't do that, they are just the first two processes that came to mind that I would like to slap.

The modern web console view is shown below, this is applicable to cloud deployments only as of the time of writing.

[![WEM Memory Optimization Web]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-mem-management-web.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-mem-management-web.png)

**Multi-Session Optimization** is quite a handy addition to the stack, allowing you to apply optimizations only when sessions are in a disconnected state. This is a very powerful feature and one that I have been using since it came out.

**Citrix Optimizer** integration was one of the first feature enhancements that started to bring WEM into the Citrix world, though to be fair, it's not really a feature that adds a lot of value unless you are post-optimizing images created by third-party tools and not including optimization as part of your build (which we would hope is not a real thing).

## Security Features

WEM moved forward heavily in the security space. When we wrote the original series, AppLocker support had just been released in addition to basic process whitelist and blacklist. Things got a lot smarter from there. A summary below of what's available:

-  [Application Security](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/security.html#application-security) is simply AppLocker with more fine-grained control delivered by WEM.
-  [Process Management](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/security.html#process-management) is a whitelist/blacklist stamp on processes.
-  The [privilege elevation](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/security.html#privilege-elevation) feature lets you elevate the privileges of non-administrative users to an administrative level necessary.
-  [Process hierarchy control](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/security.html#process-hierarchy-control) controls whether certain child processes can be started from their parent processes in parent-child scenarios.
-  [Auditing](https://docs.citrix.com/en-us/workspace-environment-management/current-release/user-interface-description/security.html#auditing-user-activities) of everything associated with elevation is captured and viewable in the WEM console.

## Actions, Conditions, Rules and Filters

There have been some minor changes in action types, primarily the biggest addition was that of Action Groups. These things reduced the assignment complexity by allowing you to define a grouping of actions (get it?) and then apply this group to users.

A prime use case of this was handling GPO migrations into WEM actions. I've worked on numerous projects where sucking in a GPO to WEM actions, and then Action Groups provided a nice easy way to organise and apply settings.

**File Type Associations** got fixed! Previously due to Microsoft changing the ball game with Modern OS, WEM was not able to process FTA properly and we had to use tools like [SetUserFTA](https://jkindon.com/file-type-association-with-wem-and-setuserfta/). The WEM team, as per usual, fixed this and we can now process FTA assignments selectively on modern OS all from within the system natively. Huzzah.

**External Tasks** became the beneficiary of a load of enhanced triggers, allowing for all sorts of advanced functions and trigger points. An External Task for WEM was pretty much the go-to for anything not native in the product (PowerShell Scripts etc), so the ability to execute these on a list of predefined triggers in the on-prem world, or more advanced triggers in a cloud deployment (think scheduled triggers or windows event log triggers etc)

[![WEM Memory External Task]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-external-task.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-external-task.png)

Custom triggers can be defined in the Cloud Console for WEM service deployments

[![WEM Trigger Web]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-trigger-web.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-trigger-web.png)

Filters and Conditions are updated to understand modern Operating Systems all the way up to Windows 11 and Windows Server 2022

[![WEM Filters]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-filter-modern-os.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-filter-modern-os.png)

For Service deployments, OR filtering is now available at the filter level (previously you had to get funky with the conditions) with a nice web model to help you understand the overall impact and end state.

[![WEM OR Filtering]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-web-or-filtering.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-web-or-filtering.png)
wem-web-or-filtering

## Group Policy Management

Lots of enhancements and changes with group policy and WEM capability. First, I think it's important to note the amount of work and tooling that Arjan Mensch created which laid the foundations for what we now have in the product (his stuff still has more advanced features). His work [which can be found here](https://github.com/msfreaks/Citrix.WEM) was the first real way of being able to export content from a GPO, convert them into WEM actions, and then import them into WEM. It was massively impressive work and really allowed some crazily complex migration projects to go smoothly.

Some of that work, conceptually at least, is now in the product natively. The "Migrate" option in the console is specifically designed to import a backed-up GPO and convert supported settings into WEM actions. From there you can suck them in via the import option.

[![WEM GPO Conversion]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-gpo-conversion.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-gpo-conversion.png)

With the new **Group Policy Settings** action type, you can define a "group policy" that will be assigned to either users or computers. For on-premises deployments, these group policies are effectively a collection of registry-based settings. Below is an example of FSLogix settings being deployed by WEM using the registry-based settings GPO type:

[![WEM GPO Actions]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-gpo-actions.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-gpo-actions.png)

For Computer objects to get group policy settings applied (which is handled by the service, not the agent), the machine objects must live in an Active Directory Group.

With the WEM Service deployment, you can target Azure Active Directory groups also.
For WEM Service customers, they get template-based GPO Settings which are ADMX settings, the same as you would get in a normal GPO object. You can import your own (for example, FSLogix) or you can use the built-in templates which are kept up to date by the WEM Service.

[![WEM GPO ADMX Template]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-gpo-admx-template.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-gpo-admx-template.png)

Looking at the same policy we referenced above using registry-based configurations, we can see that template / ADMX-based configurations are way better.

[![WEM ADMX Modern View]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-admx-modern-view.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-admx-modern-view.png)

WEM Service also supports non-domain joined machines out in the wild (think Intune-managed devices etc). WEM GPO processing offers a stack more control and assignment options for these devices, allowing for the same management tooling across all devices.

## WEM, UPM, FSLogix and Process Insights

This is still something that is worded badly all the time when some people are talking to customers or prospects – WEM is NOT a profile management tool. It drives Citrix Profile Management configuration as well as enhances FSLogix with some visibility and reporting functionality.

WEM sits under the same team as CPM within Citrix and is tightly coupled, however, it is not a profile management solution. In fact, I still do not drive CPM configuration via WEM anywhere, preferring Citrix Policy to drive that bus for several reasons that aren't in the scope of this post. So, for anyone calling WEM a profile management tool, please stop it. 

Container reporting is cool, for on-prem deployments there are some baseline FSLogix and CPM Container insights, and with the WEM Service, there is enhanced visibility into application and optimization reporting.

Worth turning all these features on and checking them out.

## Agent Auto Update Functionality

For persistent VDI workloads, managing agents meant having 3rd party tooling in the mix to push these upgrades out. The WEM team brought this capability into the Cloud Service natively, meaning that for persistent VDI, WEM can self-update and manage its agent releases.

[![WEM Auto Update]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-auto-update.png)]({{site.baseurl}}/assets/img/wem-advanced-guidance-2023/wem-auto-update.png)

## CVAD integration, and then not?

Some experiments were had, and only some are still in play.

The agent installer was combined with the CVAD installation media, and then quickly backed out – that plan didn't really work out too well.

There are configuration set assignment integrations for the WEM Service with Catalogs in CVAD, but I haven't had a lot of luck with this. OU assignments tick the box and never fail, so this is the model I run with.

## The community initiative to product gap closes

There have been a few community initiatives started to try and fill in some gaps in the product, but things changed, and the product got better, below is a list of known initiatives and their current relevance:

-  The [WEM hydration kit](https://jkindon.com/wem-hydration-kit/) – A quick population tool. This is still of use to get you up and going quickly, supported on both on-prem and service deployments.
-  The [Citrix.WEMSDK PowerShell Module](https://msfreaks.wordpress.com/2020/02/17/citrix-wemsdk-powershell-module-for-citrix-wem/) by Arjan Mensch. This was a beast of a module that was (and is) filling a huge gap in the automation world for WEM, however, it was halted due to the progression of the solution and the amount of time it took to maintain and test. It still does a great job on most configuration points for on-prem deployments but is no longer maintained.
-  A GPO conversion PowerShell Module for Citrix WEM by Arjan Mensch. Still, one of the most useful tools out there, whilst mostly replaced with WEM functionality inbuilt, it still fills in the gaps and offers more advanced migration functionality.
    -  [PowerShell Module for Citrix WEM – Part 1 – Application actions](https://msfreaks.wordpress.com/2017/12/19/powershell-module-for-citrix-wem-part-1-application-actions/)
    -  [PowerShell Module for Citrix WEM – Part 2 – GPO Import and more](https://msfreaks.wordpress.com/2017/12/27/powershell-module-for-citrix-wem-part-2-gpo-import-and-more/)
    -  [PowerShell Module for Citrix WEM – Part 3 – EnvironmentalSettings and MicrosoftUsvSettings from GPO and much, much more](https://msfreaks.wordpress.com/2018/01/08/powershell-module-for-citrix-wem-part-3-environmentalsettings-and-microsoftusvsettings-from-gpo-and-much-much-more/)
    -  [PowerShell Module for Citrix WEM – Part 4 – Import Published applications](https://msfreaks.wordpress.com/2019/01/25/powershell-module-for-citrix-wem-part-4-import-published-applications/)
-  The [WEM Documentation Script](https://jkindon.com/citrix-wem-documentation-script-v2/) – This stopped in line with Arjans PowerShell Module halting. The database got too complex to work against and there was no access to the Cloud Service, so this tooling effectively stopped.
-  [WEM Startup Scripts](https://jkindon.com/citrix-wem-updated-start-up-scripts/) – these are now defunct if you configure your environment properly.
-  A [PowerShell Script to selectively delete the user tracking cache](https://jkindon.com/selective-deletion-of-the-wem-actions-tracking-cache/) – This is now defunct and part of the product natively.

I've seen some posts online about using WEM to manage start menus combined with FSLogix AppMasking. My advice here is to simplify where you can. Mixing and matching tools to manage start menus isn't the best idea. Stick with a solution and manage it accordingly, my personal preference is AppMasking the entire thing, and using machine-level configurations. Additionally, so we are clear, WEM pinning to the start menu (tiles) is still not something that is reliable. This, again, is not a WEM problem, it's a Microsoft issue.

## Troubleshooting known problem scenarios

Without going too far into the weeds (you can lead the horse to water….), here are some high-level considerations/advice on how/where/why things may be a challenge in some environments:

-  If you don't understand how the cache works, and its impact on the processing of the environment, then you are doomed. Go read and learn it.
-  If you deploy the WEM Service and don't understand the networking requirements, you are again in some trouble. Go read and learn it.
-  If you don't understand the context in which WEM action processing takes place, then you are fighting an uphill battle (hint, it's the user context).
-  If you choose to deploy CPM configurations via WEM and haven't dealt with Cache and startup considerations, then it's on you if things don't work. Citrix policy never has a challenge…
-  If you make dumb AD decisions, [then WEM can be a victim, not the problem](https://jkindon.com/not-so-hidden-tax-citrix-wem/). Go fix it.
-  If you do silly things like AppData redirection, then WEM can be impacted. Stop it.
-  If you do silly things like Start Menu redirection on Modern OS, then go slap yourself.
-  If you don't know how to enable logging, read log output or where to start…then go read more, the first series still has plenty of valid getting started considerations.

## Summary and Closing

In the first series of articles, we made statements about WEM being almost the poor cousin to the list of AppSense etc, however, over time those gaps have closed making WEM a first-class citizen. Whilst not everything is available, the WEM team is constantly looking for ways to improve the solution, so if anything is not there, that you think should be, feel free to get in touch and we can get it on the list.

Stay up to date with changes in the solution, I track feature releases below as a starting point, however, RTFM never goes astray:

-  Citrix WEM Service [The Evolution of Citrix Workspace Environment Management Service (jkindon.com)](https://jkindon.com/the-evolution-of-citrix-wem-service/)
-  Citrix WEM On-Prem [The Evolution of Citrix Workspace Environment Management (jkindon.com)](https://jkindon.com/the-evolution-of-citrix-wem/)
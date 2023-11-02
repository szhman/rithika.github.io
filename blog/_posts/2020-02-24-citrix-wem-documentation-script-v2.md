---
layout: post
title: "Citrix WEM Documentation Script v2"
permalink: "/citrix-wem-documentation-script-v2/"
description: Documenting Citrix Workspace Environment Management - Better than last time
categories: [Citrix, PowerShell, WEM]
redirect_from: 
    - /2020/02/24/citrix-wem-documentation-script-v2
    - /2020/02/24/citrix-wem-documentation-script-v2/
image:
  path: /assets/img/citrix-wem-documentation-script-v2/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

This script is pretty much useless these days, the product evolved with too much complexity to map. This is a historical post.
{:.warning}

WEM has offered a world of fun when it comes to environment management, however it introduces a black hole when it comes to documentation. Once your configuration is in WEM, it's almost impossible to document, and not the easiest to track for complex deployments.

A while back I wrote a [very rudimentary script](https://jkindon.com/2017/12/10/wem-documentation-script-update/) that would utilise SQL queries to rip data out of the WEM database directly, and output to a pretty unstructured and nasty HTML output - it wasn't pretty, my code was pretty sh1te, but it was something.

A couple of years on, my friend [Arjan Mensch](https://twitter.com/menschab) has released the big daddy of [PowerShell modules for WEM](https://msfreaks.wordpress.com/2020/02/17/citrix-wemsdk-powershell-module-for-citrix-wem/), allowing for full end to end PowerShell based control of every single aspect of the solution. I have been lucky enough to be involved in the project since the first concept of the "WEM Commander" came to life, filling holes and gaps in the product which looked pretty damn cool! We have watched it move from a GUI based utility with some slight performance hassles, to an entire PowerShell SDK which offers so much control its ridiculous. You can read up on Arjan's work [here](https://msfreaks.wordpress.com/) (there are a load of gems).

Given I have been working with Arjan for a while, I have been developing a new [documentation script](https://github.com/JamesKindon/CitrixWEMDoc_V2/) based on the new WEM SDK Module, which I am pushing live as of today for general consumption and utilisation. The new rewrite of this script is based off two community modules; the [Citrix.WEM.SDK](https://www.powershellgallery.com/packages/Citrix.WEMSDK) module to output the data from the WEM side of things, and the awesome [PScribo](https://www.powershellgallery.com/packages/PScribo) module for outputting the data into a structure word document (without the need for Word). 

[![Verbose Output]({{site.baseurl}}/assets/img/citrix-wem-documentation-script-v2/PS.png)]({{site.baseurl}}/assets/img/citrix-wem-documentation-script-v2/PS.png)

[![Word Output]({{site.baseurl}}/assets/img/citrix-wem-documentation-script-v2/Doco.png)]({{site.baseurl}}/assets/img/citrix-wem-documentation-script-v2/Doco.png)

This script is a beast, given that at the end of the day we are pulling data directly out of SQL, there was a world of pain mapping _every. single. setting._ in the WEM console to it's appropriate value within the Database. I found all sorts of fun lying around in the DB schema, settings never implemented, settings on the way, forgotten bits of settings that _maybe-could-of-should-of-might-be_ implemented in WEM at some point one day..... ***Shrugs***. 

[![Example Advanced Settings]({{site.baseurl}}/assets/img/citrix-wem-documentation-script-v2/AdvancedSettings.png)]({{site.baseurl}}/assets/img/citrix-wem-documentation-script-v2/AdvancedSettings.png)

It supports multiple configuration sets, and allows you to list them if you aren't sure who is what, and what ID is who 

[![List Config Sets]({{site.baseurl}}/assets/img/citrix-wem-documentation-script-v2/ConfigSetsPS.png)]({{site.baseurl}}/assets/img/citrix-wem-documentation-script-v2/ConfigSetsPS.png)

My code is what it is, this script is completely open to improvements via [github](https://github.com/JamesKindon/CitrixWEMDoc_V2/). Go hell for leather with feedback or suggestions, or submit pull requests with your changes. 

I hope the hours of head scratching and trawling through tables helps people out, it has been tested up to WEM 1912 release and captures all configurations associated with all features. 

Enjoy

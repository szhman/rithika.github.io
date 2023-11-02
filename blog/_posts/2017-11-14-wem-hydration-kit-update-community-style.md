---
layout: post
title: "WEM Hydration Kit Update – Community Style"
permalink: "/wem-hydration-kit-update-community-style/"
description: "Hydration kit additions by the community"
categories: [CCitrix, WEM]
redirect_from: 
    - /2017/11/14/wem-hydration-kit-update-community-style
    - /2017/11/14/wem-hydration-kit-update-community-style/
image:
  path: /assets/img/wem-hydration-kit-update-community-style/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

Nothing beats a bit of joint "beefing up" of a tool set with community input in my books.

If you had a chance to look at my post around [FSLogix and Office Online](http://jkindon.com/2017/10/26/fslogix-or-office-online-selective-use-in-non-persistent-environments/), you will see that I spent a bit of time utilising WEM to try and make the experience almost identical from a look and feel perspective with Microsoft Online Apps and Microsoft Office Traditional Installs - This update covers all those items and more.

You will need to update a couple of URL's specifically for your environment (OneDrive etc) however these are marked in the release notes. Icons are random for portals, I chose random system icons - customise as you see fit.

The Kit master has been updated to include all of these Actions, and there is a separate "module" which you can import as a standalone addition called "Web Shortcuts" (yep, thought of that myself...).

[![URLS]({{site.baseurl}}/assets/img/wem-hydration-kit-update-community-style/URLS.png)]({{site.baseurl}}/assets/img/wem-hydration-kit-update-community-style/URLS.png)

As an even cooler addition, the WEM Jedi himself [Hal Lange](https://twitter.com/hal_lange) has been kind enough to ship some across some of the Actions he uses to avoid some annoyances and unknown side effects of certain settings within WEM (such as hiding the run command and the actual impact of that change if driven via WEM). I highly recommend watching the [CUCG recording](https://www.mycugc.org/p/do/sd/sid=312) of Hal, Steve and Ryan's presentation on WEM if you haven't yet done so. The  term "Neuter Ourselves" when dealing with windows is now part of my vocabulary.

Hal's additions have been merged in with the master file (which can be used to update your existing environment also), and his components identified in the release notes.

Kit has been updated and is available from the same location [here](https://github.com/JamesKindon/WEMHydrationKit), if there are more ideas floating around, keep them coming
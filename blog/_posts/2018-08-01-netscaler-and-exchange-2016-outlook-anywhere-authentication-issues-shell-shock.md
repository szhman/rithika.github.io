---
layout: post
title: "NetScaler and Exchange 2016 Outlook Anywhere Authentication Issues – Shell Shock"
permalink: "/netscaler-and-exchange-2016-outlook-anywhere-authentication-issues-shell-shock/"
description: "Hidden shell shock nasties with Exchange 2016 and NetScaler"
categories: [Exchange, NetScaler]
redirect_from: 
    - /2018/08/01/netscaler-and-exchange-2016-outlook-anywhere-authentication-issues-shell-shock
    - /2018/08/01/netscaler-and-exchange-2016-outlook-anywhere-authentication-issues-shell-shock/
image:
  path: /assets/img/netscaler-and-exchange-2016-outlook-anywhere-authentication-issues-shell-shock/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

On a recent engagement deploying NetScaler 12.0 Load balancing for Exchange 2016, we stumbled across an issue whereby when proxying Exchange 2010 mailbox connections via the NetScaler load balanced Exchange 2016 Servers using RPC/HTTP, the connections would hang for an extended duration (timeout settings on the VIP) before falling back to RPC. When bypassing the NetScaler and going direct to the Exchange 2016 Servers there is no problem. The offending traffic seems to be that with Authentication encapsulated within.

We searched high and low, checked Exchange end to end, and rebuilt all sorts of load balancing options. Eventually logged a ticket with Citrix and was lucky enough to work with one of their NetScaler guns, who after multiple tracing sessions identified the NetScaler appearing to not continue sending authentication payloads.

Long story short, the configuration was still utilising the old [Shell Shock Responder Policy protection method](https://support.citrix.com/article/CTX200277) which is now defunct in NetScaler 11.(something) onwards. Issue was that the packet sizes holding Auth were big enough to trigger the responder which had an action of DROP. Remove the responder, welcome back Exchange RPC/HTTP.

There are plenty of posts out there with similar issues, hopefully this helps someone else identify their problem if they have the same.

There is a quick publish article [here](https://support.citrix.com/article/CTX235592) outlining the issue and resolution.

---
layout: post
title: "Azure AD and Citrix Workspace SSO"
permalink: "/azure-ad-and-citrix-workspace-sso/"
description: "Single Sign on not playing nicely with Azure AD and Citrix Workspace"
categories: [Azure, Citrix, Cloud, Identity, Workspace]
redirect_from: 
    - /2019/09/20/azure-ad-and-citrix-workspace-sso
    - /2019/09/20/azure-ad-and-citrix-workspace-sso/
image:
  path: /assets/img/azure-ad-and-citrix-workspace-sso/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

For those customers integrating Azure Active Directory with Citrix Workspace as their primary Identity Provider (IdP), there are some changes that have been implemented which will result in some potentially unwanted behaviours in relation to the sign-on experience.

**Update 26.25.2022** This feature has finally become an opt-in opt-out setting within Workspace
{:.attention}

[![WorkspaceFedControl]({{site.baseurl}}/assets/img/azure-ad-and-citrix-workspace-sso/WorkspaceFedControl.png)]({{site.baseurl}}/assets/img/azure-ad-and-citrix-workspace-sso/WorkspaceFedControl.png)

The expected behaviour would be as follows:

### Scenario 1

I attempt to access my Citrix Workspace URL. I am redirected to Azure Active Directory for Authentication. I successfully Authenticate and am redirected back to my Citrix Workspace. I am presented with my resources.

This works as expected.

### Scenario 2

I have authenticated to an existing O365 or Azure AD provisioned resource such as the Microsoft Apps Portal ([https://myapps.microsoft.com](https://myapps.microsoft.com)). I navigate to my Citrix Workspace URL to access my resources. The expected behaviour here would be that my previous authentication is honoured, and I am passed into Citrix Workspace seamlessly.

This behaviour does <u>not</u> occur by default currently. You are instead required to re-authenticate via Azure Active Directory.

### Resolution

I recently spoke to the Product Manager of the Identity team around this behaviour and was granted permission to share the following in relation to this behaviour:

> "The Citrix Cloud Engineering Team recently made a change with its Azure AD integration to resolve a security concern. This is because Citrix Cloud customers have reported issues in certain cases when Citrix Workspace sessions time-out. The IdP of record, Azure AD, was not properly terminating its session in all cases. Because this IdP session was not terminated, a user that had a valid session with Azure AD would not be asked to re-authenticate when accessing Citrix Workspace. <br> <br> To ensure that a user is properly authenticated when accessing Citrix Workspace, the Citrix Cloud Engineering team has added the "prompt=login" parameter to every authentication request to the IdP of record. This parameter forces the user to be prompted for authentication whenever there is not a valid Citrix Workspace session. This was done to align with Industry-standard security practices. <br> <br> This change also affects customers that use ADFS with Azure AD. Microsoft has documented [how Azure AD should be configured for applications that use "prompt=login"](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/ad-fs-prompt-login) <br> <br> Customers should consult Microsoft on what setting makes sense for their environment and security posture. <br> <br> We apologize for any inconvenience this has caused as we work to continuously provide our customers with the best security and user experience."

Fear not, this change is not set in stone and you can, as a customer request that Citrix reverts this change for your tenancy by exception. You will need to provide your Org ID to Citrix Support who will process the request through to the Identity team.

I have requested that this feature be made optional moving forward with customer’s allowed to toggle this on and off as they see fit for their business. This request is currently sitting with the Identity team.

Hopefully, this will shed some light around the behaviours associated with SSO when integrating Citrix Workspace with Azure Active Directory. A big thank you to Oscar Day at Citrix for taking the time to run through the changes and detail around why they have been implemented.

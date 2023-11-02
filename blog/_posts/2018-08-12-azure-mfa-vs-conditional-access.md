---
layout: post
title: "Azure MFA vs Conditional Access"
permalink: "/azure-mfa-vs-conditional-access/"
description: "Differencing behaviour between Azure MFA and Azure AD Conditional Access"
categories: [Azure, MFA, NetScaler, Office365]
redirect_from: 
    - /2018/08/12/azure-mfa-vs-conditional-access
    - /2018/08/12/azure-mfa-vs-conditional-access/
image:
  path: /assets/img/azure-mfa-vs-conditional-access/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

I wanted to take the time to clarify a few bits that have bitten some customers around the Azure MFA, Azure MFA for Office 365 and Conditional Access side of things and how they fit together

## Azure MFA for Office 365

[Azure MFA for Office 365](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-mfa-licensing) is not the same as "full" Azure MFA or Microsoft Azure Conditional Access. They may achieve the same basic result depending on the service in question, but they are different entitlements with different purposes and different scopes 

[![users-mfa]({{site.baseurl}}/assets/img/azure-mfa-vs-conditional-access/users-mfa.png)]({{site.baseurl}}/assets/img/azure-mfa-vs-conditional-access/users-mfa.png)

Azure MFA portal Access
{:.figcaption}

Azure MFA for Office 365, which is driven out of the [MFA Portal](https://account.activedirectory.windowsazure.com/usermanagement/multifactorverification.aspx) is the free offering available to all office 365 Customers. Its purpose is to protect your Office 365 Services using basic step up authentication. It provides services such as app passwords to get past applications that do not support modern authentication, which is not the most pleasant of all user experiences, and can have the security teams a little nervous. If you want to do more than just simple Office 365 based service protection, you need to move to a paid subscription of Azure MFA

## Azure MFA (Full)

Azure Multi-Factor Authentication offers a rich set of capabilities. It provides additional configuration options via the [Azure portal](https://portal.azure.com/), advanced reporting, and support for a range of on-premises and cloud applications. Azure Multi-Factor Authentication is included in [Azure Active Directory Premium plans](https://www.microsoft.com/cloud-platform/azure-active-directory-features), and can be deployed either in the cloud or on-premises. Azure Conditional Access will utilize the Azure MFA Service when called upon

[![Azure MFA Advanced]({{site.baseurl}}/assets/img/azure-mfa-vs-conditional-access/Azure-MFA-Advanced.png)]({{site.baseurl}}/assets/img/azure-mfa-vs-conditional-access/Azure-MFA-Advanced.png)

Azure MFA Server Advanced Options
{:.figcaption}

## Azure Conditional Access

Azure Conditional Access is a service that requires an entitlement attained by either an Azure MFA Sku, EMS or AD Premium. It is the solution that allows you to write advanced conditions on any number of different scenarios, and can be extremely broad, or fine grained.

Conditional Access extends your authentication requirements out to federated 3rd party services so that you can protect not only your own assets, but access to those assets hosted in a SaaS style model. Conditional Access is not just Multi Factor Authentication. It can build access policies based on device management status (Intune or 3rd party MDM), application type, or a combination of many factors.

## Registration of Credentials

What is extremely important to note, is that if you enable MFA via the MFA portal, you completely rub out the ability to utilize Conditional Access Policies. You must have the Azure MFA user state set to disabled, and a CA policy configured to require multi factor authentication for CA based settings to apply

[![Azure MFA]({{site.baseurl}}/assets/img/azure-mfa-vs-conditional-access/Azure-MFA.png)]({{site.baseurl}}/assets/img/azure-mfa-vs-conditional-access/Azure-MFA.png)

Azure MFA - Free - Disabled State
{:.figcaption}

Microsoft [provide some detail](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-userstates) on the enrollment process and status when using Azure MFA

[![Azure MFA States]({{site.baseurl}}/assets/img/azure-mfa-vs-conditional-access/Microsoft-Enrolment-Process.png)]({{site.baseurl}}/assets/img/azure-mfa-vs-conditional-access/Microsoft-Enrolment-Process.png)

Azure MFA States
{:.figcaption}

Registration into MFA is handled by either enabling MFA (Free) for the user, or making the user subject to a conditional access policy requirement. Both of these conditions will trigger the wizard for the user to enroll and manage their Authentication methods.

[![Azure CA]({{site.baseurl}}/assets/img/azure-mfa-vs-conditional-access/Azure-CA.png)]({{site.baseurl}}/assets/img/azure-mfa-vs-conditional-access/Azure-CA.png)

Azure Conditional Access Policies
{:.figcaption}

> Handling this process via Azure Conditional Access will not change the state in Azure MFA, it will still show as disabled for the user
{:.warning}

## Word of Warning for NetScaler deployments

When deploying Multi Factor with NetScaler against Azure MFA via either the NPS Extensions (RADIUS) or SAML against ADFS or Azure AD, it's important to consider the impacts of Conditional Access vs Azure MFA. If you have plans, or your clients have plans to leverage the capability of Conditional Access moving forward, then its best to setup from the word go via Conditional Access Policies

[George Spiers](https://twitter.com/JGSpiers) has a good article [here](https://www.jgspiers.com/authentication-to-netscaler-using-ad-fs-4-0-server-2016-citrix-fas-azure-mfa-azure-cloud/) for ADFS end to end

I had a write up [here](https://jkindon.com/2018/03/05/azure-mfa-nps-extensions-with-netscaler-nfactor-authentication/) on using the NPS extensions a little while back

[Aaron Parker](https://twitter.com/stealthpuppy) has another nice write up [here](https://stealthpuppy.com/netscaler-azure-ad-conditional-access/)
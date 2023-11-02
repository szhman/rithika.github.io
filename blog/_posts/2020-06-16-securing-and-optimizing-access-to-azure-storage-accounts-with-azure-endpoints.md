---
layout: post
title: "Securing and Optimizing Access to Azure Storage Accounts with Azure Endpoints"
permalink: "/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/"
description: Securing and optimizing flows with Service and Private Endpoints
tags: [Apps and Desktops, Azure, Citrix, Cloud, FSLogix, Storage Accounts, UPM, Windows]
categories: [Apps and Desktops, Azure, Citrix, Cloud, FSLogix, Storage Accounts, UPM, Windows]
redirect_from: 
    - /2020/06/16/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints
    - /2020/06/16/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/
image:
  path: /assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

When working with Azure files, it is important to ensure that traffic destined for your files shares is both secured and routed in an optimal fashion. There are several options for securing and changing network flows for services in Azure, with a focus on Azure files, below is a breakdown of the options and some considerations.

## Service Endpoints and Firewalling the Azure Storage Account

By default, an Azure storage account is configured with a "public endpoint" and is open to traffic sourced from anywhere. The storage account is available and accessible over the public internet (this changes slightly within the Azure fabric, but for the purposes of this article, assume it's the same).

A public endpoint is not necessarily a bad thing depending on your requirements, however, it is important to be aware that unless you lock the account down, it is open by default.

[![Storage Account Firewall]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/StorageAccountFirewall.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/StorageAccountFirewall.png)

Storage accounts provide an inbuilt firewall, allowing you to restrict access to specific source IP addresses, and/or Azure virtual networks and subnets.

[![Allowed Subnets via Firewall]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/AllowedSubnets.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/AllowedSubnets.png)

When securing a storage account and restricting to an Azure Virtual network, you will need to be leveraging [Service Endpoints](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-service-endpoints-overview) on virtual networks (VNets). VNet service endpoints provide secure and direct connectivity to Azure services via the Azure backbone network. Endpoints lockdown critical Azure service resources to only specific virtual networks.

[![Service Endpoints on the VNET]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/ServiceEndpointsVNET.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/ServiceEndpointsVNET.png)

When enabling Service Endpoints and accessing a Storage Account, traffic to the storage account is switched to use IPv4 rather than public addresses (NAT) as per the below image

[![Microsoft Image on Service endpoint IP changes]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/MicrosoftImageServiceEndpoints.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/MicrosoftImageServiceEndpoints.png)

This is typically perfect for say, FSLogix Containers that are accessed from Azure VM's only. You can lock down the storage account to allow traffic only from specific subnets on your VNET whilst leveraging an optimal path to the Storage Account over the Azure fabric.

Be aware that when you lock down the storage accounts, you will lose access to view data on the shares from either the Azure Portal, or Azure Storage Explorer unless you allow the public IP you are coming from. Luckily, the Storage Account firewall detects what this is, and offers a nice easy "apply" option. If you have a dynamic IP address, don't forget to remove your exemption once you have finished your tasks.

[![Locked out of Azure file shares in the portal]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/LockedOut.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/LockedOut.png)

## Service Endpoint Policies

[Virtual Network (VNet) service endpoint policies](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-service-endpoint-policies-overview) effectively allow you to control specifically which Storage Accounts are accessed over the service endpoint, and which are not. This gives much more granular security control for protecting data exfiltration from your virtual network.

[![Service Endpoint Policy]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/ServiceEndpointPolicy.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/ServiceEndpointPolicy.png)

## Private Endpoints

You may not want any form of public endpoint connection to Azure Storage accounts, this may be a security mandate, and can be addressed specifically with the use of Azure Private Endpoints.

Azure Private Endpoint is a network interface that connects privately (as in IPv4) to a service backed by [Azure Private Link.](https://azure.microsoft.com/en-us/services/private-link/) Private Endpoint uses a private IPv4 address from an existing VNet, effectively bringing the service (in this context, storage account) into the VNet itself. If you have an ExpressRoute or IPSec VPN connection into Azure, you can also leverage the private endpoint, however DNS is king, and can be a little tricky to get working.

The image below is taken straight from the [Microsoft documentation](https://azure.microsoft.com/en-us/services/private-link/#features) and outlines nicely how the Azure Private Link solution fits together.

[![Microsoft Image on Private Link]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/MicrosoftImagePrivateLink.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/MicrosoftImagePrivateLink.png)

In my demo account below, I have blocked any form of public endpoint connections to my storage account via the use of the Firewall (note that there is no need for firewall configurations when using private endpoints)

[![Azure Storage Account Firewall]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/StorageAccountFirewall.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/StorageAccountFirewall.png)

I have also configured and connected a private endpoint to my storage account:

[![Private Endpoint on the Storage Account]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateLinkPrivateEndpoint.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateLinkPrivateEndpoint.png)

This endpoint is configured with an IPv4 address in one of my existing subnets, which is also routable over my IPSec VPN tunnel:

[![Endpoint IPv4 detail]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateLinkEndpointIPV4.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateLinkEndpointIPV4.png)

The Private Link Center shows the connections are active:

[![Azure Private Link Center]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateLinkCentre.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateLinkCentre.png)

When connecting to a private link resource using a fully qualified domain name (FQDN) it is critical to correctly configure DNS settings to resolve to the allocated private IP address. It is well worth reading through the [detail from Microsoft](https://docs.microsoft.com/en-us/azure/storage/common/storage-private-endpoints).

An [Azure Private link DNS zone](https://docs.microsoft.com/en-us/azure/private-link/private-endpoint-dns#azure-services-dns-zone-configuration) has been configured to handle Azure DNS (this helps Azure switch the DNS response from the default public IP, back to a private IP owned by the endpoint, based on the source of the request – a private endpoint doesn't mean you can't use a public at the same time), to note, my VM's in Azure leverage my existing Active Directory based DNS servers, so there were a few additional steps to achieve correct DNS lookups. [Azure wants you to use a forwarder from a VM in Azure to it's hosted DNS servers](https://docs.microsoft.com/en-us/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder), however this didn't suit me, so DNS being DNS, I manipulated it accordingly. Not posting the steps here as I don't want to push potentially bad practices, but can share on request (and you can probably track through in the screenshots anyway).

[![Azure Private DNS]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateDNS.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateDNS.png)

The network interface associated with the private endpoint contains the information required to configure DNS, including FQDN and private IP addresses allocated for a given private link resource.

[![DNS Details for the endpoint]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateDNSEndpoint.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateDNSEndpoint.png)

When using private endpoints for Azure services, traffic is secured to a specific private link resource. The platform performs an access control to validate network connections reaching only the specified private link resource.

Below shows my on-premises server, its name resolution when accessing the storage account, and the associated SMB connection to prove the point – all private.

[![Private endpoint in action 1]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateEndpointAction1.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateEndpointAction1.png)

[![Private endpoint in action 2]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateEndpointAction2.png)]({{site.baseurl}}/assets/img/securing-and-optimizing-access-to-azure-storage-accounts-with-azure-endpoints/PrivateEndpointAction2.png)

Private endpoints aren't always the best solution and need to be thought through. Potentially think about a use case where an isolated DR environment is brought online (via ASR), and accesses in read only mode, existing FSLogix Containers stored in an Azure Storage account. Given that the DR environment is 100% isolated, private endpoints are not suitable, however with Service Endpoints, we can securely provide access across the Azure fabric.

There is a cost associated with each private endpoint, so you will need to factor this into your decision making process.

## Summary

Endpoints are a key component to securing and optimising traffic flow when dealing with Azure Storage Accounts. With the recent GA announcements associated with Azure Files and Active Directory Domain Join capability, Azure Files will become more and more prevalent for EUC based workloads such as FSLogix Containers, Citrix Profile Management and alternate file services, it will be important to ensure that this data is both secure, and accessible in an optimal fashion.

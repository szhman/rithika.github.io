---
layout: post
title: "Deploying Citrix ADC's in Microsoft Azure – ADC HA Availability Set"
permalink: "/deploying-citrix-adcs-in-microsoft-azure/"
description: "Deploying Citrix ADC's in a more advanced fashion for Azure"
categories: [ADC, ARM, automation, Azure, Citrix, Cloud, NetScaler]
redirect_from: 
    - /2021/04/25/deploying-citrix-adcs-in-microsoft-azure
    - /2021/04/25/deploying-citrix-adcs-in-microsoft-azure/
image:
 path: /assets/img/deploying-citrix-adcs-in-microsoft-azure/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## ADC HA Availability Set Template

This post will focus on deploying Citrix Application Delivery Controllers (ADCs) in Microsoft Azure using the pre-defined **Availability Set** deployment using a [3 Network Interface (NIC), 3 IP model.](https://docs.citrix.com/en-us/advanced-concepts/implementation-guides/citrix-adc-vpx-on-azure-disaster-recovery-deployment-guide.html#multi-nic-multi-ip-architecture-three-nic)

To address a load of frowning ADC people before they get grumpy, I do not for a second claim to be an ADC guy, nor do I typically think that HA for ADC deployments are a good use of the appliances (GSLB is much nicer), however I am a consultant who has been through a few of these, so here are my learnings should customers wants to to deploy a HA pair in Microsoft Azure.

One of the challenges with base template currently is that it deploys all public IP and Azure ALB components at the **basic** [Sku](https://docs.microsoft.com/en-us/azure/load-balancer/skus), which quite frankly is nasty to work with. Basic load balancers are limited in their connectivity and reachability capability in advanced networking configurations, are slow and do not offer the monitoring capability you are likely wanting. As such, you are best off switching to a **standard** [Sku](https://docs.microsoft.com/en-us/azure/load-balancer/skus) load balancer.

The Availability Zone template already includes some of the relevant changes (such as deploying the Azure Load Balancer (ALB) and public IP addresses at the appropriate Sku).

Default output of the ADC HA Availability Set Template:

-  1 x Availability Set
-  2 x VMs (ADC Appliances)
-  2 x OS Disks
-  1 x Azure Public Load Balancer (basic)
-  6 x Network Interfaces across three unique subnets (Management, Frontend, Backend)
-  6 x Network Security Groups, 1 per interface
-  3 x Public IP Address (2 of these are for management and need to be killed asap)
-  1 x Storage Account for diagnostics

Each ADC is configured with the defaults below:

-  1 x Management (NSIP) interface (with a public IP attached)
-  1 x SNIP for Frontend traffic
-  1 x SNIP for Backend traffic
-  1 x VIP for the public IP that is housed on the Azure LB (the ADC owns this IP, the ALB activates it on the fabric)
-  HA using Independent Network Configuration (INC) mode

[![ADC HA Configuration in INC mode]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/HA_Overview.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/HA_Overview.png)

Out of the box, the template caters for a single load balanced public facing VIP. Given that most use cases require additional load balancing internally, you typically need an additional internal Azure Load Balancer.

## Why the Azure Load Balancer?

There are only two ways of making an IP address “live” on the Azure fabric. Either assigning the IP address onto a network interface itself, or by assigning to an Azure Load Balancer. The challenge with assigning an IP address onto a NIC, is that it can only live on one NIC and as such, we lose the ability to float that IP across two ADC’s. This is why we utilise an Azure LB, and specifically, we configure its rules with floating IP enabled, also known as Direct Server Return.

[![ALB Floating IP/Direct Server Return]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/DSR.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/DSR.png)

There is a custom probe configured on the Azure Load Balancer to probe the SNIP of the ADC on TCP port 9000. Only the active node will respond on TCP 9000, and as such, the ALB forwards traffic to the active node of the ADC pair. Should the ADC failover, the secondary SNIP will respond, and traffic is redirected accordingly.
  
## Why another Azure Load Balancer?

Well this one is simple. The rules of public inbound (such as a Gateway) are the same for internal inbound to the ADC for services such as StoreFront, or callback Gateways etc. We want to deploy an Internal Azure LB, and probe the backend SNIP to find out who is active and who is not, we then create rules to allow traffic to pass accordingly.

This typically results in a model like below:

[![High Level overview of the deployed architecture]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/FinalState.jpg)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/FinalState.jpg)

## ADC Independent Network Configuration (INC) Mode

INC mode for the HA pair is critical for ADC operations in Azure. The following snippet from Citrix docs describes the why:

> *In the HA-INC mode, the SNIP address of the ADC-VPX-0 and ADC-VPX-1 VMs are different while in the same subnet, unlike with the classic on-premises ADC HA deployment where both are the same. To support deployments when the VPX pair SNIP is in different subnets, or anytime the VIP is not in the same subnet as a SNIP, you must either enable Mac-Based Forwarding (MBF), or add a static host route for each VIP to each VPX node.*


[![HA INC Mode]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/HA_Overview.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/HA_Overview.png)

If you rebuild the HA for any reason, and forget to configure INC mode, your probes from the ALB will fail.

## ADC Routing

ADC routing in Azure tends to be unique in each deployment, but there are a basic set of rules and considerations to watch for. By default, ADC’s in Azure are configured with Mac Based Forwarding (MBF), a feature most of us would typically be used to ignoring outside of explicit scenarios. Additionally, you will find that the default route is configured to use that of the gateway associated with the **management interface**, or in ADC terms, NIC01-0 and NIC01-1 accordingly.

You will quickly want to be aligning your ADC routing for internal subnets to that of your Azure methodology. Typically, we tend to send the default APIPA ranges back to a firewall

-  10.0.0.0, 255.0.0.0, **GatewayIP**
-  172.16.0.0, 255.240.0.0, **GatewayIP**
-  192.168.0.0, 255.255.0.0, **GatewayIP**

Do not forget the rules of Azure route tables! Many deployments will be utilising multiple vnets, vnet peering, BGP and all sorts of route propagation controls. Using the effective routes view on each NIC, can quickly identify where routing challenges lay, and why things may not quite be what you expect.

The golden rule in Azure: a user defined route will always override a system defined route. In simple terms, you will need to tell Azure exactly what to do, if you want ADC backend traffic traversing a firewall (NVA).

## Network Security Groups (NSG's)

The majority of Azure work I do consists of assigning NSG’s to the subnet level. ADC deployments differ in that with the default template, you get 6 interfaces and 6 corresponding NSG’s, assigned at the interface level.

The ADC’s are going to receive traffic inbound from the internet via the Azure Public Load Balancer. This will be the second network interface or nic11-0 and nic11-1 for the respective ADC. This NIC needs to have the appropriate inbound port rules defined to allow traffic to flow to the ADC. These configurations are unique to each interface, so you will need to ensure you make these changes on both to cater for a failover.

[![Network Security Groups – per Interface]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/NSG.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/NSG.png)

The end to end flow of opening up a flow to the ADC tends to look like this:

-  Associate an IP Address with the Frontend load balancer (make it live on the fabric)
-  Create a rule on the Azure Load Balancer defining a frontend port and a backend port. This effectively opens up the port on the Azure Load Balancer and send its to the backend port which is the ADC VIP. This must have floating IP enabled
-  Add the IP address to the ADC and define it as a VIP (vip, gateway, content switch etc). Normal rules of ADC configurations apply
-  Define your inbound rules on the appropriate NSG for the appropriate interface to let the traffic pass
-  Test open ports and throw through traffic [Open Port Check Tool – Test Port Forwarding on Your Router (yougetsignal.com)](https://www.yougetsignal.com/tools/open-ports/)

## ARM Template

The default arm template does not support (as at time of writing) deploying a standard Azure Load Balancer. It deploys a basic. Conversely, the Availability Zone template deploys a standard to support AZ requirements, so a bit of hacking and cracking, and I have altered the default Citrix Availability Set ARM template to use a standard Azure LB. I am also requesting that Citrix make this change, or at least make it an option

To support this change, I needed to alter a few things. In the parameters file, I needed to alter the IP address SKU to be standard rather than basic (you cannot mix and match these things)

I needed to add three more params

```json
            "lbsku": {
            "type": "string"
        },
        "lbtier": {
            "type": "string"
        },
        "publicIpsku": {
            "allowedValues": [
                "Basic",
                "Standard"
            ],
            "type": "string",
            "metadata": {
                "description": "Sku for Public IP Address"
            }
        }
```

I also needed to alter the Public IP Sku to use the parameter

```json
        "sku": {
            "name": "[parameters('publicIpsku')]"
        }
```

Additionally, I needed to add the load balancer Sku and Tier into the ALB block

```json
        "sku": {
            "name": "[parameters('lbsku')]",
            "tier": "[parameters('lbtier')]"
        },
```

In my parameters file, I needed to add the new params we defined

```json
     "lbsku": {
            "value": "Standard"
        },
        "lbtier": {
            "value": "Regional"
        },
        "publicIpsku": {
            "value": "Standard"
        }
```

The ARM template is [available here](https://github.com/JamesKindon/Citrix/tree/master/Azure/ADC%20ARM%20Template%20-%20HA%20AS%20-%20Standard%20ALB) if you would like to use it

Before you can deploy the template, you will need to accept the marketplace terms via PowerShell. The below should take of this for you

```Powershell
Get-AzMarketplaceTerms -Publisher "citrix" -Product "netscalervpx-130-byol" -Name "netscalervpx-130-byol" | Set-AzMarketplaceTerms -Accept
Get-AzMarketplaceTerms -Publisher "citrix" -Product "netscalervpx-130" -Name "netscalervpx-130" | Set-AzMarketplaceTerms -Accept
Get-AzMarketplaceTerms -Publisher "citrix" -Product "netscalervpx-130" -Name "netscalervpx-130-byol" |Set-MarketplaceTerms -Accept
Get-AzMarketplaceTerms -Publisher "citrix" -Product "netscalervpx-130-byol" -Name "netscalervpx-130" | Set-AzMarketplaceTerms -Accept
Get-AzMarketplaceTerms -Publisher "citrix" -Product "netscalervpx-130" -Name "netscalerbyol" | Set-AzMarketplaceTerms -Accept
```

For your ARM deployment, it’s pretty simple

```Powershell
New-AzResourceGroupDeployment -ResourceGroupName RG-AE-ADC -TemplateFile "C:\Users\James Kindon\OneDrive\Azure\ADC\ARM Template ADC\template_std.json" -TemplateParameterFile "C:\Users\James Kindon\OneDrive\Azure\ADC\ARM Template ADC\parameters.json"
```

## Monitoring

One of the benefits of using a standard Azure ALB, is the monitoring that comes with it. You can view almost real time metrics on traffic flows, and probe health etc all from within Azure Monitor.

[![Azure Monitor]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/Monitor1.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/Monitor1.png)

The below image outlines my Azure Load Balancers:

[![ALB Monitors]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/Monitor2.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/Monitor2.png)

Along with a nice view of how traffic will flow based on probe status:

[![Probe Status]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ProbeStatus1.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ProbeStatus1.png)

Note that in the below image, my VPX-1 appliance is active and responding:

[![Detailed Probe Status]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ProbeStatus2.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ProbeStatus2.png)

[![HA Status]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/HA.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/HA.png)

When I fail the ADC over there will be an interim delay (the data doesn’t update in realtime)

[![Probe Status during failover]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ProbeStatus3.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ProbeStatus3.png)

[![HA Status 2]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/HA2.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/HA2.png)

[![Probe Status – Healthy Node]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ProbeStatus4.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ProbeStatus4.png)

We can also drill down to see specific config flows and status:

[![Probe Status - Traffic Flow]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ProbeStatus5.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ProbeStatus5.png)

Over time we can start trending the availability and behaviours of the ADC and view the traffic flow distribution. We always expect to see one node taking the majority of traffic

[![Probe Status Trending]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/Metrics1.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/Metrics1.png)

Under our detailed metric, overview, we will always only ever see a single node available also – these statistics are aggregated log analytics data

[![Historical Availability]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/Metrics2.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/Metrics2.png)

Network Virtual Appliances (ADC’s) also show up nicely in Azure Monitor, however it is not supported to push the more advanced monitoring agents onto them unfortunately

[![NVA Monitoring]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/NetworkHealth.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/NetworkHealth.png)

And of course, for the best solution around, you can always integrate your ADC’s with ControlUp and gain some insight into what’s going on within the appliances themselves:

[![ControlUp in action]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ControlUpADC.png)]({{site.baseurl}}/assets/img/deploying-citrix-adcs-in-microsoft-azure/ControlUpADC.png)

## Summary and Key Takeaways

Deploying ADCs into Microsoft Azure is always a fun adventure. The first time you do it, it can be overwhelmingly complex.

-  It is important to leverage the tools available in Azure to gain an insight into how your ADCs are performing, and how they related components are interacting
-  By simply deploying the correct Sku types, you get the above information and detail automatically along with a far more management capability and speed of execution
-  For those of us dealing with customer facing environments, engage the networking teams who manage the Azure landscape early, you will be thankful you did
-  HA INC mode is critical in Azure – if you misconfigure it, goodnight
-  NSG configurations will trick you at least once, check them with a fine tooth comb
-  Azure Routing is key – the normal rules apply to ADC interfaces and not all is always as it seems
-  MBF is enabled by default

Good luck.
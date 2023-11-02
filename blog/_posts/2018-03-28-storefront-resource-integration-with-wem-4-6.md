---
layout: post
title: "Storefront Resource Integration with WEM 4.6"
permalink: "/storefront-resource-integration-with-wem-4-6/"
description: "Delivering Citrix Published Applications via Citrix WEM"
categories: [Citrix, Start Menu, WEM, Windows, XenApp]
redirect_from: 
    - /2018/03/28/storefront-resource-integration-with-wem-4-6
    - /2018/03/28/storefront-resource-integration-with-wem-4-6/
image:
  path: /assets/img/storefront-resource-integration-with-wem-4-6/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

One of the cool new features of WEM 4.6 is the ability to directly call Storefront resources as WEM applications and push into relevant Start Menu's or desktops the same as you would any other WEM driven application. We have been able to achieve the same result previously by utilising the receiver *selfservice.exe* with a *launch* switch, but this integrates a little nicer.

There are a few things to note as pre-requisites and a few things to consider before you run off conquering giants and all that.

-  The Server hosting the WEM console you are adding StoreFront applications from must have Citrix Receiver installed
-  WEM will publish the application shortcut regardless of the user having access to the Published Application. It may make sense to align your WEM assignments to your application publishing groups
-  You will want to think about how you display the WEM Agent for published applications, else you can end up with duplicates which whilst makes sense to us, may confuse users as below - hiding the Agent for published applications may make a little more sense than usual

[![Image1_DoubleUp]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image1_DoubleUp.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image1_DoubleUp.png)

There are a few new items in the WEM console to support this new functionality **Advanced Settings -> Storefront** Here you can add, edit and remove StoreFront Stores

[![Image2_Storefront]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image2_Storefront.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image2_Storefront.png)

The WEM Application dialog box has also been redesigned

[![Image3_AppActionLayout]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image3_AppActionLayout.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image3_AppActionLayout.png)

## Configuring the Integration

The flow to enable this new functionality is as follows

1.  Enable your storefront stores within the WEM Console Itself
2.  Create WEM Applications based on these stores

**Advanced Settings -> Storefront -> Select Add** Enter your StoreFront Store (you will need a URL per Store)

[![Image4_StoreAddition]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image4_StoreAddition.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image4_StoreAddition.png)

Select **Add** and **Apply** your Config. Navigate back to **Actions -> Applications** and **add** a new Application Select the Application Type as "**Storefront Store**"

[![Image3_AppActionLayout]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image3_AppActionLayout.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image3_AppActionLayout.png)

Select the Store you defined previously and select **Browse**

[![Image5_AppAdd1]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image5_AppAdd1.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image5_AppAdd1.png)

Note at this point receiver will kick in an enumerate resources from the store you have defined. This is actioned via Citrix Receiver

[![Image6_AppAdd2]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image6_AppAdd2.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image6_AppAdd2.png)

From here you can select any of your published applications and create a WEM application action from them

[![Image7_AppAddStoreEnum]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image7_AppAddStoreEnum.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image7_AppAddStoreEnum.png)

Give it the normal details and enable self-healing if you need

[![Image8_AppAddDetails]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image8_AppAddDetails.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image8_AppAddDetails.png)

I build out start menu's, and you can add the Application the same as any other

[![Image9_StartLayout]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image9_StartLayout.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image9_StartLayout.png)

Carry out your assignments (Take note of the first point discussed above - align this to a group that has access to the application via Citrix)

[![Image10_Assignment]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image10_Assignment.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image10_Assignment.png)

Refresh the WEM agent on your published desktop

[![Image11_Desktop1]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image11_Desktop1.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image11_Desktop1.png)

Note that the icon is a bit narky - this is pulled from the cache in receiver on the server with the WEM console, so is worth changing to something nicer manually under the application properties in WEM

[![Image12_Icon1]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image12_Icon1.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image12_Icon1.png)

Default crappy icon
{:.figcaption}

[![Image13_Icon2]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image13_Icon2.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image13_Icon2.png)

Nicer not crappy icon
{:.figcaption}

Refresh your agent again....Much nicer

[![Image14_Desktop2]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image14_Desktop2.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image14_Desktop2.png)

Launch the application

[![Image15_LaunchApp]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image15_LaunchApp.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image15_LaunchApp.png)

As expected - a nicely launched published app

[![Image16_Proof]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image16_Proof.png)]({{site.baseurl}}/assets/img/storefront-resource-integration-with-wem-4-6/Image16_Proof.png)

## Conclusion

This is probably the first true bit of integration with existing Citrix components we have seen. Admittedly its simply utilising receiver but it’s a start. Hopefully it’s a start of many more convergences with the existing product set

(Dear Citrix, please integrate more stuff)

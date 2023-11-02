---
layout: post
title: "Citrix MCS and Azure Shared Image Gallery"
permalink: "/citrix-mcs-and-azure-shared-image-gallery/"
categories: [Citrix, CVAD, Azure, Windows, MCS, SIG, Provisioning]
redirect_from: 
    - /2021/07/11/citrix-mcs-and-azure-shared-image-gallery
    - /2021/07/11/citrix-mcs-and-azure-shared-image-gallery/
description: Road testing Citrix Machine Creation Services with Azure Shared Image Gallery Integration
image:
  path: /assets/img/citrix-mcs-and-azure-shared-image-gallery/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro to the Shared Image Gallery

Citrix MCS is growing constantly in the Azure space, one of the new features made available quietly of late, is the ability to consume the Azure Shared Image Gallery for storing images.

The Azure Shared Image Gallery, or SIG for short offers several advantages associated with versioning and image replication, allowing you to version control and replicate images around the globe, using a structured methodology and consuming the Azure backbone. You can read more on the Azure SIG [here](https://docs.microsoft.com/en-us/azure/virtual-machines/shared-image-galleries)

This is the first iteration of SIG support with MCS, and comes at the moment, with the following use cases and detail:

-  Accelerates the creation and hydration of Operating System disks
-  Enables deployment of Ephemeral Disks (blog post to follow)
-  Lays the foundations for more advanced future state architecture such as single image replication and multi catalog consumption, reducing complexity around how images will be managed and deployed in multi region Azure environments. This involves a lot more than what is currently released

There are no other current benefits of using a SIG with MCS right now. This will of course change as the solution matures, but for now, a lot of what Microsoft offers as an advantage to using a SIG, is not consumable by MCS. Yet.

[![SIG]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/share-image-gallery.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/share-image-gallery.png)

Currently, SIG deployments with MCS have the following characteristics

-  MCS creates and manages a SIG per catalog with the following elements:
    -  **A Gallery**. Images are stored here. MCS creates one gallery for each machine catalog.
    -  **A Gallery Image Definition**. This definition includes information (operating system type and state, Azure region) about the published image. MCS creates one image definition for each image created for the catalog
    -  **A Gallery Image Version.** Each image in a Shared Image Gallery can have multiple versions, and each version can have multiple replicas in different regions. Each replica is a full copy of the published image. Citrix Virtual Apps and Desktops service creates one Standard_LRS image version (version 1.0.0) for each image with the appropriate number of replicas in the catalog’s region, based on the number of machines in the catalog, the configured replica ratio, and the configured replica maximum

-  An existing catalog can be converted from a snapshot catalog to a SIG based catalog, and then back to a snapshot catalog as required – no re-deployment of the catalog is required, however an update of the catalog is required to trigger image prep tasks
-  The SIG only supports managed disks
-  For image replica ratios, the default value for persistent OS disks is 1000 and the default value for non-persistent OS disks is 40
-  Azure currently supports up to 10 replicas for a gallery image single version
-  Each Catalog update creates a new image definition and an image version – this includes initial build and any updates
-  Each MCS driven version is flagged as version 1.0.0\. There is no versioning available. This can be very confusing it you look at the SIG itself, however in the context of what the SIG is currently used for with MCS, it’s actually irrelevant

## SIG considerations

-  In a non-persistent environment, the SIG replica to machine ration equates to a maximum optimized count of 400 concurrent machines. This does not mean you can’t have more than this, its just optimal boot performance at the 400, anything more and it will be less than optimal. You can read up on [SIG scaling here](https://docs.microsoft.com/en-us/azure/virtual-machines/shared-image-galleries#scaling)
-  SIG image versions are billed based on consumed size, they are billed on a snapshot logic, however they are billed per replica. MCS has brains to clean up unused SIG versions behind the scenes, however it doesn’t appear to currently clean definitions
-  Deleting a Catalog does not remove the deployed SIG

## Deploying a SIG based catalog

Deploying into a SIG is simple as outlined below (I have purposely demo’d the below using the GUI)

Selection master image as per normal – OS Disk, or snapshot in Azure

[![Golden Image Selection – Disk or Snapshot]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreate.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreate.png)

Select your Disk type and Licence. Select "Place Image in Shared Image Gallery"

[![Disk Type and SIG Selection]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreateSIG.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreateSIG.png)

Choose your machine type, optionally choose Availability Zone (awesome addition) and then expand your SIG settings. Typically, I would image the defaults are fine for most use cases

[![Configure your Machine Type, Zone Config and SIG Details]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreateSIGDetail.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreateSIGDetail.png)

If you are of the mind to consume MCSIO, go for it

[![Writeback Cache Settings]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreateSIGMCSIO.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreateSIGMCSIO.png)

Choose your Resource Group in Azure

[![Empty Resource Group selection]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreateSIGRG.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreateSIGRG.png)

Specify the normal AD bits and bobs

[![Catalog AD Details]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreateSIGAD.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogCreateSIGAD.png)

Give your Catalog a name and away you go

The deployment time can take a little longer than usual as your image is uploaded into a new created SIG. Note that on completion, my resource group contains a new SIG named after my catalog, and Image Definition and an image version. For the keen eye, notice also there are no more storage accounts used (yet another enhancement) and your identity disks are now created as standard SSD to reduce costs (and another cool enhancement…)

[![Azure Shared Image Gallery Creation]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/AZResourceGroupSIG1.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/AZResourceGroupSIG1.png)

My SIG has an image based on my chosen OS disk

[![Image Definition]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/AZResourceGroupSIG2.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/AZResourceGroupSIG2.png)

I also have a single version created

[![Version 1.0.0 – all versions are 1.0.0]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/AZResourceGroupSIG3.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/AZResourceGroupSIG3.png)

[![Version Detail]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/AZResourceGroupSIG4.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/AZResourceGroupSIG4.png)

My version has a single replica in the region I deployed my catalog

[![Replica Detail]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/AZResourceGroupSIG5.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/AZResourceGroupSIG5.png)

Note that my catalog still shows my initial OS disk based image. Note there is currently absolutely no information to outline any form of relationship between the SIG and the Catalog. It is 100% invisible.

[![Catalog Detail for Selected Image]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogDetail1.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogDetail1.png)

## BYOD SIG

Currently, you can pull an image from an existing SIG as below. If you are automating image builds into a SIG via Packer etc, this can be beneficial. MCS will take this image, and suck it into it’s own managed SIG.

[![Selecting an image from an existing SIG]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/BYODSIG.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/BYODSIG.png)

## Updating a SIG based catalog

So what happens when we update a catalog using a SIG? Below I have a newly minted snapshot (vs OS disk) in Azure, this is just a personal preference as you have control over naming standards and detail

[![Azure Snapshot Update]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogUpdate1.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogUpdate1.png)

A new image definition and version is created in the existing SIG associated with the Catalog

[![New Definition and Version Creates (note its another 1.0.0 version)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogUpdateSIG1.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogUpdateSIG1.png)

Note that the SIG has next to no detail outlining what is what. The only way you really know what is in use, is looking at the most recent date on the image

[![Updated SIG detail – no identifier on the version]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogUpdateSIG2.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogUpdateSIG2.png)

My catalog will show my Azure snapshot – no reference to any SIG component in the current release

[![Azure Snapshot identified on the Catalog]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogUpdateDetail.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogUpdateDetail.png)

## Rolling back a SIG based catalog

We can follow up the usual rollback methodology which will revert back to the last known image as per below

[![Rollback to last known Disk]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogRollback1.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogRollback1.png)

We cannot however, rollback or forward by selecting the SIG. By current design, the SIG is tagged to hide itself from Studio. It’s effectively and invisible entity

[![The existing SIG is hidden]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogRollbackHiddenSIG.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/CatalogRollbackHiddenSIG.png)

The usual Citrix Tagging is used to hide the resources – you probably don’t want to screw with these too much (I did and found some quirks)

[![Tagging hides resources from MCS]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/SIGTags.png)]({{site.baseurl}}/assets/img/citrix-mcs-and-azure-shared-image-gallery/SIGTags.png)

## Summary

As stated initially, this is a nice step in the right direction, and whilst those with SIG experience with WVD or Scale Sets etc. may scratch their heads at it’s current use, it is iteration number 1 with a lot of enhancements and changes to come. Importantly, anything that improves boot times in VDI environments running on Azure is a win (it allows for more aggressive Autoscale logic) and critically, it is the backbone for ephemeral disk consumption, which is a huge win moving forward.

Many thanks as always to [Yuhua Lu](https://www.linkedin.com/in/yuhua-lu-39b1551/) over at Citrix, who has fielded question after question on how things fit together, and what comes next

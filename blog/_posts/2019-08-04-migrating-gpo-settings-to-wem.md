---
layout: post
title: "Migrating GPO settings to WEM"
permalink: "/migrating-gpo-settings-to-wem/"
description: "A walkthrough of the new WEM capability to deliver GPO settings"
categories: [Apps and Desktops, Citrix, Cloud, WEM, Windows]
redirect_from: 
    - /2019/08/04/migrating-gpo-settings-to-wem
    - /2019/08/04/migrating-gpo-settings-to-wem/
image:
  path: /assets/img/migrating-gpo-settings-to-wem/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

A consistent challenge when beginning the adoption of WEM services into an existing environment, is the analysis and migration of existing GPP and GPO settings into the WEM way of processing. Moving policies full of printers, drive maps, logon scripts, files and folder options along with registry keys, be it single or collection based, is typically the longest part of getting WEM to really shine in an environment.

Last week marked the release of a [new feature in the WEM Cloud Service](https://docs.citrix.com/en-us/workspace-environment-management/service/whats-new.html) which allows a direct import of a GPO backup file (or files) into the WEM service. The process is a simple one to drive, but pretty complicated behind the scenes. The basic process is as follows:

-  Backup your existing GPO or GPO objects into a single Zip File. (Make sure your backups are only one folder deep in the zip file)
-  Import the ZIP file into the WEM service using the new Migrate option
-  Choose whether the import everything or create an import file (using the standard VUEM xml format). I suggest the latter
-  Import the XML file based actions and settings
-  Assign to your AD objects

## Working Example

In the below example, I am exporting two GPO objects, these contain a load of preferences and environmental settings that WEM could also process, along with folder redirection settings.

Group Policy Backup:

[![PolicyBackup]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/PolicyBackup.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/PolicyBackup.png)

GPO Backup File

[![PolicyBackup2]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/PolicyBackup2.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/PolicyBackup2.png)

Logging into to the WEM Cloud Service, I have created a new blank Configuration Set for demo purposes. Nothing has been enabled, no templates or hydration kits imported

Blank WEM Config Set

[![ConfigSet]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ConfigSet.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ConfigSet.png)

You will first need to upload your ZIP file via the HTML client. This will dump the zip file in the default upload folder

Upload via HTML 5 Client

[![Upload]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/Upload.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/Upload.png)

Choose Existing Archive

[![Upload2]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/Upload2.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/Upload2.png)

The new Migrate button is available on the home ribbon next to the usual backup and restore suspects shown above. Select the Migrate button and choose your ZIP file

Select uploaded ZIP file

[![ImportFile]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFile.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFile.png)

Choose the convert option and give your job a name. I cannot see a time where you would ever want to simply overwrite – I would always want to sanity check what I am pulling in

Choose Convert and specify a Name

[![ImportFile2]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFile2.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFile2.png)

Select start Migration. If your ZIP file is in the correct format, you should have a nice clean migration with confirmation shown as per below

[![Conversion]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/Conversion.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/Conversion.png)

You can now view a summary of what's been imported

A few cool things have happened at this point. Note in the below screen dump, any GPP based items have been successfully pulled from my GPO backup files

[![GPPimportPreview]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/GPPimportPreview.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/GPPimportPreview.png)

Secondly, note that any supported environmental and USV (folder redirection) settings have also been pulled

[![SettingsImportPreview]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/SettingsImportPreview.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/SettingsImportPreview.png)

Looking closely, the keen eye may notice that the WEM import will convert an existing variable to a [WEM hashtag value](https://jkindon.com/2019/03/08/wem-variables-dynamic-tokens-hashtags-and-strings/) in the USV pathing. Note my GPO below uses `%UserName%`, WEM converts this to `##UserName##`

[![USVtoWEMHash]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/USVtoWEMHash.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/USVtoWEMHash.png)

You can now select finish and process to importing the configuration files you just created. Select the Restore Option and select the appropriate option for Actions (GPP) or Settings (Environmental, USV). You will need to do this one at a time due to the nature of the WEM import tools

[![RestoreWizard]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoreWizard.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoreWizard.png)

Choose your Import Folder created when you pulled in the GPO backup files

[![ImportFromLab]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFromLab.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFromLab.png)

All my settings from GPO are available for import

[![ImportFromLab2]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFromLab2.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFromLab2.png)

Job done. All my actions are ready to go

[![ImportFromLab3]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFromLab3.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFromLab3.png)

Import is successful

[![ImportFromLab4]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFromLab4.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFromLab4.png)

My Drives, Printers, Registry Keys, File System Objects, Environment Variables and External Tasks are all available

Network Drives:
[![Drives]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/Drives.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/Drives.png)

Registry Keys:
[![RegKeys]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RegKeys.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RegKeys.png)

External Tasks:
[![ExternalTasks]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ExternalTasks.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ExternalTasks.png)

A few things of note that I was happy with here:

1.  Drives were imported with a self-healing action enabled
2.  If a network drive had a display name defined in GPP, it was imported and used on the action in WEM. If it did not, it was left blank
3.  Printers were imported with self-healing enabled
4.  Registry items were imported cleanly. If they were part of a collection, they were split cleanly into individual actions, and no (default) keys were imported
5.  I tried to trick WEM and define a binary value key which is not supported by WEM. It was smart enough to ignore the key
6.  External tasks were sucked in from areas of the Policy such as "Logon Scripts"

Nicely done. On to settings next. Select restore, this time choose settings and select your import file again

[![ImportFromLab5]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFromLab5.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/ImportFromLab5.png)

Note this time both Environmental Settings and Microsoft USV settings are recognised and ready for import. Follow the wizard. Take note of the below warning. You want to be sure in what you are doing here

[![RestoreWarning]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoreWarning.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoreWarning.png)

This is a new config set for me, so I am happy to overwrite

[![RestoreWizard2]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoreWizard2.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoreWizard2.png)

Over into Policies and Profiles in the WEM Console, I can successfully confirm the two supported settings I had In GPO have been mirrored into WEM configurations

[![RestoredEnvSettings]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoredEnvSettings.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoredEnvSettings.png)

USV (Folder Redirection) Settings have also been moved properly with the hashtag conversion complete

[![RestoredUSV1]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoredUSV1.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoredUSV1.png)

[![RestoredUSV2]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoredUSV2.png)]({{site.baseurl}}/assets/img/migrating-gpo-settings-to-wem/RestoredUSV1.png)

Again a few things of note that I was happy with:

-  Importing the settings didn't automatically enabled the processing of Environmental Settings
-  Importing USV Settings didn't automatically enable processing of them

## Product vs Community

Pretty good result really. Now whilst this is an awesome feature for the WEM Service, it is not the first tool to allow this. My good friend [Arjan Mensch](https://twitter.com/menschab) and I spent a huge amount of time a couple of years back building a [powershell module](https://msfreaks.wordpress.com/2017/12/19/powershell-module-for-citrix-wem-part-1-application-actions/) that did this, and much much more. By we, I mean mostly him, I just provided the chaos policies and a shopping list of cool things, he did the rest – and man does it rock. If you haven't seen it, used it, or checked it out and are working in the business of implementing WEM for customers, you are shooting yourself in the foot.

In addition to the basic functionality above, Arjan's module can do the following

-  Import all actions and relevant settings (inc USV) from existing GPO objects
-  Control the all options associated with the actions on import – self healing, descriptions, run once, state, names, prefixes etc. All of them.
-  Import Drives and Printers from a CSV file
-  Identify and export GPP based Item level targets to a CSV file
-  Import applications from an existing redirected Start Menu (old school in 2008 R2) and then mirror that configuration in WEM apps
-  Import applications from an existing folder, or set of folders by converting all executables to WEM applications
-  [Convert Studio Published Apps to WEM applications](https://jkindon.com/2019/03/23/studio-apps-to-wem-to-address-citrix-session-sharing-challenges/) via CSV export and Import

Read the blog series below for details:

-  [Part 1: Application Actions](https://msfreaks.wordpress.com/2017/12/19/powershell-module-for-citrix-wem-part-1-application-actions/)
-  [Part 2: GPO Import and More](https://msfreaks.wordpress.com/2017/12/27/powershell-module-for-citrix-wem-part-2-gpo-import-and-more/)
-  [Part 3: Environmental Settings and USV Import](https://msfreaks.wordpress.com/2018/01/08/powershell-module-for-citrix-wem-part-3-environmentalsettings-and-microsoftusvsettings-from-gpo-and-much-much-more/)
-  [Part 4: Convert Studio to WEM Apps](https://msfreaks.wordpress.com/2019/01/25/powershell-module-for-citrix-wem-part-4-import-published-applications/)

This tool is one of the best community driven tools out there, and Arjan has also been working on a true end to end WEM PowerShell SDK. Go check out his work, it's truly awesome and it can be used for both On Prem and Service based deployments of WEM.

## Summary

The WEM team and Arjan have individually done awesome jobs at getting this stuff cranking, both should be provided multiple beers for the time saving toolsets they have given us. Kudos!

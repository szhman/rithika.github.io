---
layout: post
title: "WEM Upgrade Process"
permalink: "/wem-upgrade-process/"
description: "Upgrading citrix WEM"
categories: [Citrix, WEM]
redirect_from: 
    - /2017/12/18/wem-upgrade-process
    - /2017/12/18/wem-upgrade-process/
image:
  path: /assets/img/wem-upgrade-process/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

With the latest release of WEM 4.5, I figured I would take the chance to document a how to guide on upgrading the WEM Components in the safest fashion.

It is **critical** that you disable Mirroring of the Database prior to any WEM upgrades.

Step 1. Backup your Environment. A simple SQL backup job is fine for the WEM DB.

[![SQL_Backup]({{site.baseurl}}/assets/img/wem-upgrade-process/SQL_Backup.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/SQL_Backup-name.png)

And I like to snapshot my broker server/s.

[![Snapshot]({{site.baseurl}}/assets/img/wem-upgrade-process/Snapshot.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Snapshot-name.png)

Step 2. Document your WEM Infrastructure Service Configuration. This is wiped after an upgrade (Note this is the last time you will need to do this via the GUI, moving forward you can use PowerShell)

Launch the Infrastructure Service Configuration:

`C:\Program Files (x86)\Norskale\Norskale Infrastructure Services\Norskale Broker Service Configuration Utility.exe`

Take note of the following Settings in particular Database, Network Settings (if you customised ports), Advanced Settings, Licencing. DB Maintenance most people leave as defaults 

[![ISC_DB]({{site.baseurl}}/assets/img/wem-upgrade-process/ISC_DB.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Snapshot-ISC_DB.png)

Database Settings Node
{:.figcaption}

[![ISC_Network]({{site.baseurl}}/assets/img/wem-upgrade-process/ISC_Network.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Snapshot-ISC_Network.png)

Network Settings Node
{:.figcaption}

[![ISC_Advanced]({{site.baseurl}}/assets/img/wem-upgrade-process/ISC_Advanced.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/ISC_Advanced.png)

Advanced Settings Node
{:.figcaption}

Step 3. Once you have completed your preparation steps, you can run through the simple upgrade.

Start with the Console:

`\Workspace-Environment-Management-v-4-05-00\Citrix Workspace Environment Management Console Setup.exe`

[![Wizard_Console]({{site.baseurl}}/assets/img/wem-upgrade-process/Wizard_Console.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Wizard_Console.png)

Follow the defaults, there is nothing of concern here.

Next we move to the infrastructure services

`\Workspace-Environment-Management-v-4-05-00\Citrix Workspace Environment Management Infrastructure Services Setup.exe`

[![Wizard_ISServices]({{site.baseurl}}/assets/img/wem-upgrade-process/Wizard_ISServices.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Wizard_ISServices.png)

Leave as the defaults, this just updates the actual services and we will configure shortly Once complete, launch the DB Management Utility (This can be done via PowerShell moving forward from this release) 

[![Wizard_ISServicesDB]({{site.baseurl}}/assets/img/wem-upgrade-process/Wizard_ISServicesDB.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Wizard_ISServicesDB.png)

Select to Upgrade the Database

[![DBUtil_Upgrade]({{site.baseurl}}/assets/img/wem-upgrade-process/DBUtil_Upgrade.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/DBUtil_Upgrade.png)

Make sure here to specify the Correct DB as noted in your first steps - if you have multiple (Unlikely however I do) make sure you run through this for all DB's 

[![DBUtil_Upgrade2]({{site.baseurl}}/assets/img/wem-upgrade-process/DBUtil_Upgrade2.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/DBUtil_Upgrade2.png)

Once DB has been upgraded we can move on

[![DBUtil_Upgrade3]({{site.baseurl}}/assets/img/wem-upgrade-process/DBUtil_Upgrade3.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/DBUtil_Upgrade3.png)

`C:\Program Files (x86)\Norskale\Norskale Infrastructure Services\Norskale Broker Service Configuration Utility.exe`

Repopulate with all values that you took note off in the initial tests and allow the services to restart

[![Wizard_ISServicesConfig]({{site.baseurl}}/assets/img/wem-upgrade-process/Wizard_ISServicesConfig.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Wizard_ISServicesConfig.png)

Repeat for each node in your environment Launch the console and confirm access and versions 

[![Version]({{site.baseurl}}/assets/img/wem-upgrade-process/Version.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Version.png)

As mentioned, you can now view your service configuration via PowerShell using the `Get-WemInfrastructureServiceConfiguration` Commandlet

[![Powershell]({{site.baseurl}}/assets/img/wem-upgrade-process/Powershell.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Powershell.png)

Now it's time to update your Target Agents. Note that there is no urgency here, the older agents will report into upgraded WEM with no issues

Follow whatever unsealing process for whatever provisioning technology you use Run the updated installer program - this can be run as an in place upgrade:

`\Workspace-Environment-Management-v-4-05-00\Citrix Workspace Environment Management Agent Setup.exe`

Accept defaults, there is no magic here

[![Wizard_Agent]({{site.baseurl}}/assets/img/wem-upgrade-process/Wizard_Agent.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Wizard_Agent.png)

Your WEM Services will stop and restart during the process. Once installed, you can confirm your version

[![AgentVersion]({{site.baseurl}}/assets/img/wem-upgrade-process/AgentVersion.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/AgentVersion.png)

Once installed it is always important to run the ngen drain tasks.

I personally use BIS-F for any sealing work, but the manual process is outlined below.
`C:\Windows\Microsoft.NET\Framework\v4.0.30319\ngen.exe update`
`C:\Windows\Microsoft.NET\Framework64\v4.0.30319\ngen.exe update`
`C:\Windows\Microsoft.NET\Framework\v4.0.30319\ngen.exe eqi 1`
`C:\Windows\Microsoft.NET\Framework64\v4.0.30319\ngen.exe eqi 1`
`C:\Windows\Microsoft.NET\Framework\v4.0.30319\ngen.exe eqi 3`
`C:\Windows\Microsoft.NET\Framework64\v4.0.30319\ngen.exe eqi 3`

[![ngen]({{site.baseurl}}/assets/img/wem-upgrade-process/ngen.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/ngen.png)

Once completed you can reseal your image, when the WEM agent reports in, you should now see the new version 

[![Broker_AgentVersion]({{site.baseurl}}/assets/img/wem-upgrade-process/Broker_AgentVersion.png)]({{site.baseurl}}/assets/img/wem-upgrade-process/Broker_AgentVersion.png)

And you are good to go

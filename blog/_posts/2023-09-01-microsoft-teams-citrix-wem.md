---
layout: post
title: Managing Microsoft Teams with Citrix WEM
permalink: "/microsoft-teams-citrix-wem/"
categories: [Citrix, WEM, Teams, Cloud]
description: Utilising Citrix WEM to make Teams management a bit less rubbish
image:
  path: /assets/img/microsoft-teams-citrix-wem/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

Here is a short post around a feature that has snuck into WEM without much fanfare, however will have a nice impact on customers trying to deliver, and manage Microsoft Teams in a Citrix environment.

## JSON Actions

A new type of action has been introduced (always awesome when the WEM team delivers on feature requests): [JSON Actions](https://docs.citrix.com/en-us/workspace-environment-management/service/manage/configuration-sets/actions.html#json-files). This feature, amongst other use cases, allows you to manage Microsoft Teams JSON based configuration dynmically, and reliably. It even includes a Teams Template as part of the action definition.

The new feature includes several components:

-  The ***Action*** itself. This is defined under the WEM Web Console ***Actions -> JSON Files***. You need to enable ***Process JSON Files*** on the top right pane.

[![json_actions]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/enable_json_processing.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/enable_json_processing.png)

Once you have created the action, it will appear and be assignable the same as any other action type in WEM:

[![json_action_created]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/json_action_created.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/json_action_created.png)

-  Monitoring of the Action found under ***Advanced Settings -> Monitoring Preferences -> Action Processing Results -> JSON Files***

[![json_monitoring]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/enable_monitoring_json.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/enable_monitoring_json.png)

This will, by default, upload processing data from the agent to WEM Service every 4 hours. It can be forced using the ***Retrieve statistics*** from the Agent Options in ***Monitoring > Administration > Agents***.

[![json_retrieval]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/agent_retreieve_stats.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/agent_retreieve_stats.png)

You should be aware of the configuration option under ***Advanced Settings -> Action Settings -> Other Settings -> Await policy and JSON file processing on logon***. This controls if the logon process should wait for WEM Based GPO settings *and* JSON action processing to complete before the logon is completed.

[![json_action_settings]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/json_action_settings_advanced.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/json_action_settings_advanced.png)

Importantly, from an actions processing ordering standpoint, JSON files are procesed before applications. This means you can write configurations and ensure they exist prior to the application being launched, which is nicely handled with WEM configuration.

An example of an application being configured to launch via WEM is shown below (note that this has a different name in the web console) for autostart but results in the same behaviour.

[![teams_action_auto_start]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/team_action_auto_start.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/team_action_auto_start.png)

## Teams Specific WEM Items

For Teams specific considerations, WEM has some nice options of note:

-  You will want to process the JSON **Action** in the **user** context.
-  The **desktop-config.json** file that Teams uses, does not exist until Teams launches for the first time. WEM has an option for **Create file if it does not exist** allowing you to pre-stage the file with the appropriate settings to address first run challenges (Create file if it does not exist).
-  A **backup** function to backup the JSON file ***prior*** to alteration.
-  The JSON Template itself covers 5 settings to get you going, additional can be added as required
    -  Auto-start application. `True/False`
    -  Open application in background. `True/False`
    -  On close, keep the application running. `True/False`
    -  Disable GPU hardware acceleration. `True/False`
    -  Enable spell check. `True/False`

[![teams_json_action_template]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/json_teams_action_config.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/json_teams_action_config.png)

Once you have assigned the action (presumably both the Teams Application action and the Teams JSON action), they will show up as per usual on the endpoint:

[![teams_client_processing]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/wem_client_config_vuemrsav.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/wem_client_config_vuemrsav.png)

Teams JSON files are written as expected, including backup configs if selected:

[![teams_json_processed]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/teams_json_actions_filesystem.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/teams_json_actions_filesystem.png)

The usual log files are used to track processing, along with some additional logging on the endpoint:

-  %UserProfile%\Citrix WEM Agent.log
-  %UserProfile%\Citrix WEM Agent User Session Utility - JsonFileConfigFiltering.log

You can also retrieve status from the WEM console under ***Monitoring -> Reports***

[![wem_processing_reports]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/wem_json_report_retrieve_1.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/wem_json_report_retrieve_1.png)

Detail will show what/when the JSON has been processed, or if it is pending an update:

[![wem_processing_report_detail]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/wem_json_report_retrieve_detail.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/wem_json_report_retrieve_detail.png)

Here is Teams launched for the first time in the user profile, pre-configured with the appropriate settings:

[![teams_client]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/teams_app_settings_applied.png)]({{site.baseurl}}/assets/img/microsoft-teams-citrix-wem/teams_app_settings_applied.png)

***You will need to be running agent version: `2306.1.0.1` as at the time of writing, and this is currently a WEM Service feature.***
{:.attention}

You can read more on some Teams considerations from Mr James Rankin below:

-  [Microsoft Teams on Citrix Virtual Apps and Desktops, part #1 – installing the damned thing](https://james-rankin.com/articles/microsoft-teams-on-citrix-virtual-apps-and-desktops-part-1-installing-the-damned-thing/)
-  [Microsoft Teams on Citrix Virtual Apps and Desktops, part #2 – default settings and JSON wrangling](https://james-rankin.com/articles/microsoft-teams-on-citrix-virtual-apps-and-desktops-part-2-default-settings-and-json-wrangling/)
-  [Microsoft Teams on Citrix Virtual Apps and Desktops, part #3 – 18 tips for optimizing performance](https://james-rankin.com/articles/microsoft-teams-on-citrix-virtual-apps-and-desktops-part-3-18-tips-for-optimizing-performance/)

## Summary

Whilst Teams is still a steaming pile of garbage from a management and performance perspective, the WEM team just made it suck a little bit less to manage in the enterprise. Nicely done.

Don't forget [this article](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/multimedia/opt-ms-teams.html) for anything involving Teams in Citrix.
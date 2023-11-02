---
layout: post
title: "FSLogix Container Replication with Bvckup2"
categories: [Citrix, Profiles, Windows, FSLogix, Replication, Bvckup2]
description: Utilising bvckup2 to replicate FSLogix Container workloads
image:
  path: /assets/img/fslogix-container-replication-with-bvckup2/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

Ok firstly let's deal with the elephant in the room. B-V-C-K-U-P-2. Good luck pronouncing that, it's basically backup2 (imagine an A upside down), don't even bother with trying the other one. Pre-sales, conferences, architecture meetings, it's funny as hell watching people stumble and then rage out and give up. Alex has been kind enough to aquire [https://backup2.com](https://backup2.com) so that we stop falling on our own faces regularly.

[bvckup2](https://bvckup2.com/) (backup2!) has fast become the go to for FSLogix container replication jobs. The tool is unique in its ability to not only provide a simple and easy to drive user interface, but it does magical things like replicate mounted FSLogix containers in Realtime, with no data loss…. Ridiculously powerful and efficient.

I have beaten the drum for a while with any customer looking to replicate data, however have yet to post anything that shows some of the ins and outs - so here we are, a post dedicated to container replication with bvckup2 (say that 10 times in a row).

There are two configuration points for bvkcup2

-  The GUI interface
-  The `override.ini` file for more advanced capability (one of multiple)

For any queries, the [support forum](https://bvckup2.com/support/forum/topic/438) is typically my go to in the first instance.

It's also worth noting that there are a few other awesome utilities available which are [great additions to the toolbag](https://iobureau.com/#peanuts), along with both Diskovery (a data storage inspection tool) and CCSIO (Cold-cache Sequential I/O Benchmark) [available here](https://iobureau.com/)

## Backup Settings

The basics are typically, well, basic.... Install the tool on your server that you are replicating from - this is not a hard and fast, but so far it's been the most efficient way of configuring replication jobs for me

Choose your `source` (backup from), and choose your `destination` (backup to)

Optionally configure an appropriate [service account](https://bvckup2.com/support/forum/topic/413) to access both locations (press the key...). This is important when copying permissions etc

[![job]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/job.png)]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/job.png)

`What` to backup allows you to get as selective as you like with your filtering - what to include, or what to exclude

[![filtering]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/filtering.png)]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/filtering.png)

`When` to backup is pretty self-explanatory - when do you want this happen (we will touch on some quiet time capability shortly)

### Detecting Changes

I typically select [re-scan destination](https://bvckup2.com/support/forum/topic/744) on every run

If you are using REFS, it is worth taking note of the following article and [configuration setting](https://bvckup2.com/support/forum/topic/1336)

`conf.scanning.src.requery_meta    *.VHDX` is used to force the program to see changes to mounted containers

[![rescan-destination]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/rescan-destination.png)]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/rescan-destination.png)

### Copying Options

I use delta copying for the most part, this is nice and quick

[![delta-copying]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/delta-copying.png)]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/delta-copying.png)

### Destination Deletion

Deletion of backup copies is an important setting. Remember bvckup2 is primarily a replication tool designed to keep location A in sync with location B, It will do exactly that, but has some built in mechanisms to protect from accidental deletion in the destination

-  Delete backup copies simply means keep the destination in complete sync with the source
-  Archive backup copies will create a backup of files that are deleted in the source, and thus in the destination - it’s a get out of jail free card incase you screw up the source
-  Keep backup copies will stop deletions occurring - you likely don't want this in the context of container replication

[![backup]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/backup.png)]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/backup.png)

### Processing

Bvckup2 is a multithreaded beast - executing multiple steps at once is what we want to be enabling

[![multithread]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/multithread.png)]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/multithread.png)

### Additional Options

There are also a few more advanced options to think about here

-  For shadow copying, use if needed is where you want to start (I am assuming Windows as the source)
-  You will want to copy Ownership and Security Info - this is very important for profiles, both Containers and file based

[![security]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/security.png)]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/security.png)

Logging is amazingly verbose, and surprisingly interactive within the GUI, you can view in real time what is going on with all replication jobs

[![logs]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/logs.png)]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/logs.png)

## Advanced configuration and considerations

Each backup job is stored (if in service mode) under the programdata directory (`C:\ProgramData\Bvckup2\engine\backup-0001\override.ini`). If in user mode, this is configured in the user profile.

You can read up on [service mode configurations](https://bvckup2.com/support/forum/topic/799#). This will allow you to leave your jobs unattended and operate in a shared configuration mode (multiple admins can alter the job).

Each job allows for an override.ini file to be created for more advanced replication use cases. You can read up on the use of configuration files [here](https://bvckup2.com/support/forum/topic/480). Specifically for ini files, you can read up [here](https://bvckup2.com/support/forum/topic/800)

I tend to have a couple of advanced options that I play with

| **Item** | **Detail** |
| :--- | :-- |
| `conf.scanning.src.requery_meta    *.VHDX` | Allows for working with ["live" files](https://bvckup2.com/support/forum/topic/1336). Important for REFS |
| `conf.quiet_time    2021-06-21 06:00, 13 hours` | Allows for a quiet time replication window. A quiet time is a time in which a replication job is not allowed to kick off. <br> <br> This does not stop an already started replication job, it is not a "pause" command. <br> <br> The example allows for a quiet time of 6am through to 7pm |
| `conf.preserve_newer_files   mtime` | This is what I use in migration scenarios where I have two locations in sync, and I have started to migrate users over to the destination directory. <br> <br> This configuration basically says "if you see a modified time in the destination which is newer than the source - leave it the hell alone". <br> <br> Perfect for migration scenarios |

These settings are all available in the gui for newer releases
{:.attention}

There is a nice simulation feature allowing you to view what will happen based on your current configurations

[![simulation]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/simulation.png)]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/simulation.png)

I have had many an email querying around two way replication. My default is do not do it. If you are in a situation where you are looking to do two way replication, [then FSLogix Cloud Cache is likely a smarter solution](https://jkindon.com/architecting-for-fslogix-containers-high-availability/). I have no doubt it can be technically done, and if any tool can do it, its bvckup2, but it's not something I have implemented nor felt the need to play with - happy to post any related configurations for those that have done it if they would like to share.

## Summary

A simple post, but hopefully a pointer in the right direction. I cannot speak higher of the tool or of Mr Alex Pankratov who has been an awesome support throughout many a configuration.

Give it a crack, you think its fast with containers, you should see what it can do with file data.

[![Running]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/bvckup2-screenshot.png)]({{site.baseurl}}/assets/img/fslogix-container-replication-with-bvckup2/bvckup2-screenshot.png)

Discount code available if you are interested, send me an contact request and I'll share the code. You will typically be wanting to play with Pro for Windows Server [https://bvckup2.com/features](https://bvckup2.com/features)
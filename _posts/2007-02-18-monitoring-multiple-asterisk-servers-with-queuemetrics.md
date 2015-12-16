---
layout: post
title:  Monitoring multiple Asterisk servers with QueueMetrics
date:   2007-02-18 23:12:23.0
tags:
 - QueueMetrics
 - cluster
 - Asterisk

categories: update
---

**This node refers to an EXPERIMENTAL time-limited feature present in QueueMetrics as of release 1.3.3. It is not meant to be used on production systems as it is simply EXPERIMENTAL at the moment. Use it at your own risk. A special licence will be needed  once this feature will be officially released.**


All versions of QueueMetrics up to release 1.3.2 were meant to monitor one single instance of Asterisk, that is, you simply could not use them to "mix together" two or more queue_log files coming from different servers, as the results would have been unpredictable and quite possibly wrong.

With 1.3.3, we add a new feature that makes it possible to consolidate multiple queue_log files, so that you monitor a "virtual queue" that is made up of a number of different queues residing on different physical  servers. This will work for both historical and real-time reports, and is meant to be a major step forward making it very straightforward to build and monitor clustered, highly-reliable call-centers.

To do this, we use the [qloaderd](-NOTFOUND-qloaderd) process to upload data from different servers on different partitions on the same QueueMetrics database, and then we group the queue data together when running an analysis. This means we can leverage on the existing MySQL-storage infrastructure already in place and minimize the changes to the user interface needed to implement and run this feature.

**Setting up a sample system**

In this example, we imagine setting up a demo cluster made of three Asterisk servers, called A, B and C, while QueueMetrics and its MySQL database reside on server D.

* On each Asterisk server, we install *qloaderd* to upload data to a different partition on D. So server A will upload data to a  partition named "A", server B to partition "B" and server C to partition "C".

* Once we see that the queue_log table on D is being populated by the three different servers, we use the following file-name syntax to run a report for all three servers at once: 
- *cluster:A|B|C*
If we want, we can still run a report on only one partition, by useing the standard syntax "sql:A" or "sql:B" or "sql:C".

This setup works fine provided that agents are logged on to only one server at a time (that is, if the same agent code is used through the cluster, Agent 101 first closes its affiliation with server A and then logs on to server B). There is no limit to queues, so you can (and likely should) have the same queue name on one or more servers. This way you can monitor all calls to a queue even if they happen on parallel systems.

If you want to have a setup where your agents will log on at once to all your Asterisk servers, you should tell QueueMetrics to load agent events from only one server and ignore the others. This can be obtained using a single dash after the name of each partition you don't want to read agent data from, that is:
- *cluster:A|B-|C-*
means that calls and agent events are read only from partition A, while calls only are read from B and C.

If you instead have a setup where you have agents with the same code working on different servers and they are different users, the following format should be used:
- *clustrw:A|B|C*
This means that all local agent channels are rewritten prepending the name of the partition, that is Agent/101 on server A will be seen in QueueMetrics as Agent/A-101, while Agent 101 on server B will be seen as Agent/B-101, therefore avoiding collisions.

**Open issues**

- Audio file monitoring will likely not be working much - you should create an accessible disk share mounting the recording directories of all separate servers. 
- At the moment, there is no simple way to know on which server a call was answered
- Real-time monitoring, agent log on, and all other activities obtained through a call-file or a direct manager acess do not work with clusters (that is, they work with only one server of the cluster).
- Queue restarts on one server will affect the monitoring as if they had been done on all servers of the cluster at once.

If you encounter any bugs or have suggestions for this feature, we would very much like to hear from you.


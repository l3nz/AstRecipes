---
layout: post
title:  Updating QueueMetrics using yum
date:   2008-09-10 15:14:57.0
tags:
 - yum
 - update
 - RHEL
 - QueueMetrics
 - install
 - CentOS
 - A@H

categories: update
---

If you did the original installation of QueueMetrics using yum, updating it is very easy.


* make a backup of the QueueMetrics database - just in case something goes wrong.


* make a backup of the current *web.xml* and *configuration.properties files* (if you want to know where to find them, log in QM and look for the *System folder* on the Licence page).


* type:
    
    yum update queuemetrics

Yum will download and update QueueMetrics and its dependencies


* Go to *http://myserver/queuemetrics/dbtest* and update the database if necessary


* Copy the old *web.xml* and *configuration.properties files* to the new system folder and restart QueueMetrics by typing:
    
    /etc/init.d/queuemetrics restart


Now log in to QueueMetrics and enjoy the new version.

**See also:**


* [Installing QueueMetrics using Yum]({% post_url 2006-04-21-installing-queuemetrics-using-yum %})

* [Manually updating QueueMetrics]({% post_url 2008-01-22-manually-updating-queuemetrics %})

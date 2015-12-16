---
layout: post
title:  Installing QueueMetrics using Yum
date:   2006-04-21 19:06:31.0
tags:
 - yum
 - RHEL
 - QueueMetrics
 - install
 - CentOS
 - A@H

categories: update
---


* This recipe is EXPERIMENTAL - use it at your own risk!

It is now possible to install QueueMetrics in a very automated way using Yum and Loway Research's Yum repository. 

This setup was thought of for CentOS 4 / RHEL 4, so if you use a different OS it might not work at all or in part. 

As Asterisk@Home is based on CentOS, it will work for A@H users!


This line adds Loway Research to the list of known Yum repositories:
    
    wget -P /etc/yum.repos.d http://yum.loway.it/loway.repo



And this line actually installs QueueMetrics, downloading all of its dependencies automatically.
    
    yum install queuemetrics


At the end of the setup, you have to perform the following operation manually in order to create the QueueMetrics database:
    
    cd /usr/local/queuemetrics/webapps/queuemetrics-1.1.1/WEB-INF/README
    ./installDb.sh


Do not do this operation more than once, i.e. don't do it if you already have a QM database! You will be prompted for a pair of passwords (type the passwords that are suggested), and that's all!

Point your browser to the following URL:
http://mybox:8080/queuemetrics

And you'll be ready to rock with the default users (hint: login ad *demoadmin* password *demo* if it's the first logon).

Do not forget to have a look at the online user manual located at [http://queuemetrics.loway.it/manual.jsp](http://queuemetrics.loway.it/manual.jsp).

**See also**


* [Updating QueueMetrics using yum]({% post_url 2008-09-10-updating-queuemetrics-using-yum %})

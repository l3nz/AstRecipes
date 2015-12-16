---
layout: post
title:  Yum quick-start
date:   2007-11-07 12:09:53.0
tags:
 - yum

categories: update
---

**Installing and removing**


yum install queuemetrics

yum remove queuemetrics

**Installing from a local file**


yum localinstall queuemetrics.rpm
yum localupdate queuemetrics.rpm


**Which packages match a certain name?**


yum list | grep -i queue

    
    queuemetrics.noarch              1.4.2-29         installed
    queuemetrics-java.i386           1_5_0_11-16      installed
    queuemetrics-tomcat.noarch       5.0.28-9         installed
    queuemetrics-java-debuginfo.i386 1_5_0_06-14      LowayResearch


**Show the dependencies for a given package**


yum deplist queuemetrics

**Where does a file come from?**


yum provides /usr/local/queuemetrics/tomcat/RUNNING.txt

It needs to download the filelists from each server, so it might be quite slow.

**Show package information**


yum info queuemetrics

    
    Loading "installonlyn" plugin
    Setting up repositories
    Reading repository metadata in from local files
    Installed Packages
    Name   : queuemetrics
    Arch   : noarch
    Version: 1.4.2
    Release: 29
    Size   : 1.5 M
    Repo   : installed
    Summary: Loway QueueMetrics
    
    Description:
    A queue_log analyzer for Asterisk.


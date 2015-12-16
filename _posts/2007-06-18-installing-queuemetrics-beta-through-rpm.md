---
layout: post
title:  Installing QueueMetrics beta through RPM
date:   2007-06-18 23:36:01.0
tags:
 - #frontpage
 - rpm
 - beta
 - install
 - QueueMetrics

categories: update
---

The official QueueMetrics repository contains only production-quelity versions of QueueMetrics. Still, sometimes a beta version is released in RPM format, so we need to install it manually. 

**Make sure QueueMetrics is installed through yum**

We want everything to be working before installing a beta, so we do the following steps to install the latest working version:

    
    wget -P /etc/yum.repos.d http://yum.loway.ch/loway.repo
    yum install queuemetrics


And follow the on-screen database installation instructions.

**Download and install the beta**

As the beta will be installed locally and is not a signed package, we need to modify */etc/yum.conf* and set *gpgcheck=0* in the *main* section. 

When this is done, you can download and install the package through:

    
    wget http://queuemetrics.com/download/rpm-beta/queuemetrics-1.4.0b1-20.noarch.rpm
    yum localupdate queuemetrics-1.4.0b1-20.noarch.rpm


After it's done, it's a good idea to start the automatic datbase check and upgrade utility located at *http://myserver:8080/queuemetrics/dbtest*

**Reverting to the last stable version**

If you want to get back to the latest stable version, you can simply type the following commands:

    
    yum remove queuemetrics
    yum install queuemetrics


The beta will be removed and the last stable version will be pulled from the public repository.



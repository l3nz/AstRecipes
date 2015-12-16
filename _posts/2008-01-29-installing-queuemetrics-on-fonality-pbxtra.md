---
layout: post
title:  Installing QueueMetrics on Fonality PBXtra
date:   2008-01-29 16:08:49.0
tags:
 - QueueMetrics
 - PBXtra
 - install
 - Fonality
 - #frontpage

categories: update
---

The Fonality PBXtra boxes are based on CentOS 4.2, so the yum install is feasible with some modifications. The most important thing that is missing is that those boxes use Fonality's RPM repository, where the **mysql-server**
 packages are missing.

Luckily, it is possible to download and install the correct RPM package from the CentOS vault:

    
    wget http://vault.centos.org/4.2/os/i386/CentOS/RPMS/mysql-server-4.1.12-3.RHEL4.1.i386.rpm
    rpm -i mysql-server-4.1.12-3.RHEL4.1.i386.rpm
    /etc/init.d/mysqld restart


This fetches and installs the correct MySQL server.

Then we add the Loway repository and install QueueMetrics:
    
    wget -P /etc/yum.repos.d http://yum.loway.ch/loway.repo
    yum install queuemetrics


At the end of the installation, do not follow the automated procedure to create/upload the database but type:

    
    /etc/init.d/mysqld restart
    cd /usr/local/queuemetrics/webapps/queuemetrics-1.4.3/WEB-INF/README
    mysql mysql


This will enter the MySQL shell; type the following lines:

    
    create database queuemetrics;
    grant all privileges on queuemetrics.* to 'queuemetrics'@'%' identified by 'javadude';
    grant all privileges on queuemetrics.* to 'queuemetrics'@'localhost' identified by 'javadude';
    grant all privileges on queuemetrics.* to 'queuemetrics'@'pbxtra9999' identified by 'javadude';


See the last line where it says *pbxtra9999*? change that to the actual code of the box, as shown in the command prompt.

Type *\q* to exit the MySQL shell.

Now type:

    
    mysql -u queuemetrics -p queuemetrics < queuemetrics_sample.sql


Enter *javadude* as a password when prompted.

Now go to *http://yourserver:8080/queuemetrics* and login as *demoadmin* pass *demo*.

If your server has queues and agents configured, you can import them by using the *Import wizard* voice in the main screen.

It is very likely that you will have to run a script to build a suitabe queue_log: see [Rebuilding Fonality queue_log]({% post_url 2008-02-06-rebuilding-fonality-queue_log %}).


**See Also:**


* [CentOS 4.2 RPM list](http://vault.centos.org/4.2/os/i386/CentOS/RPMS/)

* [PBXtra Home Page](http://www.fonality.com/pbxtra_features.html) 

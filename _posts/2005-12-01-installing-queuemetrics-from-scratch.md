---
layout: post
title:  Installing QueueMetrics from scratch
date:   2005-12-01 21:03:25.0
tags:
 - QueueMetrics
 - MySQL
 - #frontpage

categories: update
---

This recipe shows how to install QueueMetrics on a clean machine. 
Installing QueueMetrics comes in a number of steps:

* Obtain the latest version of QueueMetrics

* Prerequisites: Java and Tomcat

* Configuring the MySQL database

* Obtaining the MySQL connector

* Installing QueueMetrics

**Obtain the latest version of QueueMetrics**

Download the latest version of QueueMetrics from [http://queuemetrics.loway.it/download.jsp](http://queuemetrics.loway.it/download.jsp).
Download the user manual from that page too - the first part is a somehow longer guide to installing and choosing a server.

**Prerequisites: Java and Tomcat**


* Download a Java SDK from Sun. Current 1.5.0 versions are fine - you cand download them from [http://java.sun.com/j2se/1.5.0/download.jsp](http://java.sun.com/j2se/1.5.0/download.jsp). Make sure you download the SDK version, not JRE or other. 
Note that most Linuxes come with GCJ, but don't use that. 
Install the Java SDK as - say - */usr/local/j2sdk1.5.0* (follow its instructions)


* set the global environment variable JAVA_HOME to */usr/local/j2sdk1.5.0*. While we are at it, we define a starting memory allocation of 256 megabytes that can grow up to 512 megabytes for the virtual machine running Tomcat and QueueMetrics:

Execute the following commands in the shell:
    
    JAVA_HOME=/usr/local/j2sdk1.5.0
    JAVA_OPTS="-Xms256M -Xmx512M"
    export JAVA_HOME
    export JAVA_OPTS



* Download the current Apache Tomcat from [http://tomcat.apache.org/](http://tomcat.apache.org/). Do not use any prebuilt RPM version or other preinstalled packages for your distribution. Each will be differently set up. Just download the tarball from the distribution site and unpack it into */usr/local/tomcat*


* Start it by typing */usr/local/tomcat/bin/startup.sh*; test that it works by browsing *http://myserver:8080*


* When done, stop it by typing */usr/local/tomcat/bin/startup.sh*
 
**Configuring the MySQL database**

You will need a MySQL database, either local or on another machine. 


* Install MySQL 4 on your linux box using any means you like; in most cases it will be already installed


*Create a database called queuemetrics in your MySQL installation and fill it with data taken from the file *WEB-INF/README/queuemetrics.sql* obtained from the QueueMetrics tar archive.

The process will probably be something like:
- Enter your MySQL shell as root typing:
    
    mysql mysql

- Create the new database
    
    CREATE DATABASE queuemetrics;
    GRANT ALL PRIVILEGES ON queuemetrics.* TO 'queuemetrics&#8217;@'localhost' IDENTIFIED BY 'javadude'; 

- Exit the MySQL shell
- Load the database sample with something like
    
    mysql --user=queuemetrics --password=javadude queuemetrics < queuemetrics_sample.sql



**Obtaining the MySQL connector**


* Download the MySQL connector (named Connector/J) from [http://www.mysql.com/products/connector-j/index.html](http://www.mysql.com/products/connector-j/index.html). If possible, download a stable version, better if version 3.0.


* Unpack the Connector/J tarball you have downloaded and locate a file named *mysql-connector-java-3.0.10-stable-bin.jar*. It will be used later.

Other versions of the MySQL connector will likely work but might require some minor tweaking of parameters in the JDBC URI.

**Installing QueueMetrics**


* Unpack the local QueueMetrics tar file in */usr/local/tomcat/webapps* (we imagine it will be unpacked as a directory named *queuemetrics-xxx*)


* Put the Connector/J file you just downloaded in */usr/local/tomcat/webapps/queuemetrics-xxx/WEB-INF/lib* where the other JAR 
files are.


* Edit the JDBC URI in */usr/local/tomcat/webapps/queuemetrics-xxx/WEB-INF/web.xml* to point to  
your database, setting user and password too


* Restart tomcat (it may take a few seconds)


* Point your browser to *http://myserver:8080/queuemetrics-xxx* and wait for the login screen to appear
 
That's all there is for a basic install.

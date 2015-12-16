---
layout: post
title:  Installing Queuemetrics on Debian
date:   2006-12-05 11:21:10.0
tags:
 - Ubuntu
 - Tomcat
 - installing
 - Debian
 - #frontpage

categories: update
---

It is quite easy to install QueueMetrics on Debian and Debian-derived systems, like the very popular Ubuntu:


* First of all, make sure you have root privilege


* Type the following commands:
Install Sun's Java SDK:
    
    apt-get install sun-java5-jdk


Prepare the environment to run Tomcat
    
    JAVA_HOME=/etc/java-1.5.0-sun
    JAVA_OPTS="-Xms256M -Xmx512M"
    export JAVA_HOME
    export JAVA_OPTS


Now install Tomcat itself:
    
    apt-get install tomcat5


Now, the default Tomcat in Debian comes with Java security enabled. This will make it not possible to read configuration files and Asterisk log files, so it must be turned off.

To do this, edit the */etc/init.d/tomcat5*

    
    # Use the Java security manager? (yes/no)
    #TOMCAT5_SECURITY=yes (Default setting)
    TOMCAT5_SECURITY=no



* Now you have a working Tomcat and Java subsystem, they are the toughest part of installing QueueMetrics. Now you must install QueueMetrics itself:

Follow the recipe [Installing QueueMetrics from scratch]({% post_url 2005-12-01-installing-queuemetrics-from-scratch %}) from **Configuring the MySQL database**
 onwards.

Remember that to start Tomcat you must type */etc/init.d/tomcat5 start* and to stop it */etc/init.d/tomcat5 stop*.

Also remember that you should configure the environment variables  JAVA_HOME and JAVA_OPTS at boot if you want your QueueMetrics instakll to start automatically.

**Automatic startup**

The command used under debian for automatic rc startup is *update-rc.d*

**See also**


* [Generic QueueMetrics startup script]({% post_url 2008-01-09-generic-queuemetrics-startup-script %})

**Notes**

>>Thanks to Paul C. for very useful input<<



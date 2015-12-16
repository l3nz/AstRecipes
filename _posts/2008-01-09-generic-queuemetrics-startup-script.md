---
layout: post
title:  Generic QueueMetrics startup script
date:   2008-01-09 11:26:14.0
tags:
 - startup
 - rc
 - QueueMetrics
 - installing
 - Debian
 - #frontpage

categories: update
---

This is a general-use startup script that can be used to start and stop QueueMetrics on any Unix system:

    
    #! /bin/bash
    #
    # This script starts and stops QueueMetrics
    #
    
    export JAVA_HOME=/mnt/hd1/j2sdk/jdk1.5.0_06
    export JAVA_OPTS="-Xms256M -Xmx256M"
    export TOMCAT=/mnt/hd1/tomcat
    
    
    
    case "$1" in
      start)
            echo "Starting up QueueMetrics"
            echo "Using JAVA_HOME:       $JAVA_HOME"
            echo "Using JAVA_OPTS:       $JAVA_OPTS"
    
            $TOMCAT/bin/startup.sh
            ;;
    
      stop)
            echo "Shutting down QueueMetrics"
            $TOMCAT/bin/shutdown.sh
            ;;
    
      *)
            echo "Usage: $0 {start|stop}" >&2
            exit 1
            ;;
    esac
    
    exit 0


You should of course set the JAVA_HOME, JAVA_OPTS and TOMCAT variables as appropriate for your system.


**Automatic startup on Debian**


Just run:

    
    chmod 755 /etc/init.d/queuemetrics
    update-rc.d queuemetrics defaults


**Automatic startup on RedHat**

Just run:

    
    chkconfig –-add queuemetrics

To make sure it start in the right runlevels:

    
    chkconfig –-list queuemetrics

If it does not, try again with:

    
    chkconfig –-level 2345 queuemetrics on



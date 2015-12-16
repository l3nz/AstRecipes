---
layout: post
title:  Administering QueueMetrics using Tomcat
date:   2006-03-31 14:10:57.0
tags:
 - Tomcat
 - QueueMetrics
 - manager
 - #frontpage

categories: update
---

If you run QueueMetrics, it's often useful to know how many people are using the webapp at once. This recipe shows how to use the Tomcat manager interface that comes shipped with the standard Tomcat webapps to perform this task.

You have to perform the following steps:


* Edit tomcat/conf/tomcat-users.xml so that there is a user of your choice who has the role *manager*, like the user called *me* in the following example:
    
    <?xml version='1.0' encoding='utf-8'?>
    <tomcat-users>
      <role rolename="tomcat"/>
      <role rolename="role1"/>
      <role rolename="manager"/>
      <user username="tomcat" password="tomcat" roles="tomcat"/>
      <user username="both" password="tomcat" roles="tomcat,role1"/>
      <user username="role1" password="tomcat" roles="role1"/>
      <user username="me" password="yessir" roles="tomcat,manager"/>
    </tomcat-users>



* Shutdown and restart Tomcat

* Run the following command to know the active sessions for the webapp *queuemetrics* running in the Tomcat instance on the same server:
    
    links -dump http://me:yessir@127.0.0.1:8080/manager/sessions?path=/queuemetrics


The output will look something like:
    
    OK - Session information for application at context path /queuemetrics
    Default maximum session inactive interval 30 minutes
    30 - <40 minutes:54 sessions


This means that there are currently 54 active, non-expired sessions running.

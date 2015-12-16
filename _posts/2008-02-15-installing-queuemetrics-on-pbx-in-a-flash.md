---
layout: post
title:  Installing Queuemetrics on PBX-in-a-Flash
date:   2008-02-15 13:08:26.0
tags:
 - QueueMetrics
 - install
 - #frontpage
 - NerdVittles
 - PbxInAFlash
 - tutorial

categories: update
---


PBX-in-a-Flash is Nerd Vittles ultra-cool version of a preconfigured Asterisk PBX based on CentOS 5. This makes installing QueueMetrics very easy, though the fact that the standard access port is already used by a different, preinstalled package makes a bit of hand-tweaking necessary.

**Basic installation**

    
    wget -P /etc/yum.repos.d http://yum.loway.ch/loway.repo
    yum install queuemetrics


This should manually download and install  most of what you will need. When the intallation terminates, remember to follow the procedure to install the database as written on the last screen that you see; this will be something like:

    
    cd /usr/local/queuemetrics/webapps/queuemetrics-1.4.3/WEB-INF/
    ./installDb.sh


The exact path will depend on the exact version of QM being installed. When asked for the system password type **passw0rd**
 (that's a zero, not  a capital O) and when asked for the default password type **javadude**
.

Now we have to change the port that QM will use for its connection: run the following command:
**vi /usr/local/queuemetrics/tomcat/conf/server.xml**


and look for a place that looks like the following:
    
    <!-- Define a non-SSL Coyote HTTP/1.1 Connector on port 8080 -->
    <Connector port="8080"
               maxThreads="150" minSpareThreads="25" maxSpareThreads="75"
               enableLookups="false" redirectPort="8443" acceptCount="100"
               debug="0" connectionTimeout="20000"
               disableUploadTimeout="true" />


Change the part that says *Connector port="8080"* to *Connector port="8099"*. Save and exit.

Restart QueueMetrics by typing:
**/etc/init.d/queuemetrics restart**


Now we're almost ready but for one thing: the preconfigured firewall will block external access to port 8099.

You can do two things:

* Turn the firewall off altogether by issuing **/etc/init.d/iptables stop**


* Modify the firewall rules:
Enter the following command:
**vi /etc/sysconfig/iptables**


By the end of the file, before the final COMMIT statement, add the following lines:
    
    # Allow connections to our QueueMetrics server
    -A INPUT -p tcp -m tcp --dport 8099 -j ACCEPT


Restart iptables by issuing **/etc/init.d/iptables restart**
 so the new configuration can be picked up.

Now open your browser, go to **http://serveraddress:8099/queuemetrics**
 and you should see the login page. Log on as *demoadmin* password *demo*. 

**Other things you should do**


* [Turn off Asterisk queue_log rotation]({% post_url 2006-10-06-avoiding-queue_log-file-rotation %})

* [Request and install a demo licence]({% post_url 2006-12-05-upgrading-queuemetrics-licences %})


* 
* The good news: as both PBX-in-aFlash and TrixBox are based on FreePBX, the official TrixBox tutorials that you can find on QueueMetrics website apply just as well. 
* 
* 


**See also**


* [Nerd Vittles](http://nerdvittles.com/)

* [PBX in a Flash](http://pbxinaflash.com/) 


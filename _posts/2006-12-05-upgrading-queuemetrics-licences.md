---
layout: post
title:  Upgrading QueueMetrics licences
date:   2006-12-05 14:46:22.0
tags:
 - QueueMetrics
 - licence
 - #popular

categories: update
---

This recipe explains how to set a new activation key in QueueMetrics. This applies to temporary keys you may request from the web site or to official product activation keys.

To request a temporarty unlimited licence for QueueMetrics, please fill in the form at [http://queuemetrics.com/sendDemoLicence.jsp](http://queuemetrics.com/sendDemoLicence.jsp)

Once you have obtained it, to insert the key into QueueMetrics you have to: 


* Locate the file *WEB-INF/web.xml* within the QueueMetrics webapp. If you installed your copy of QueueMetrics using *yum*, the webapp is located at */usr/local/queuemetrics/webapps/queuemetrics-version*.


* Edit the file with a text editor 


* Locate the section with the licence; it looks something like:
    
    <init-param>
    <param-name>LICENZA_ARCHITETTURA</param-name>
    <param-value>...........</param-value>
    </init-param>



* Insert your licence key within the *param-value* tag, exactly as it was sent to you and all on one single line without line breaks or spaces.


* Save the file 


* Restart your servlet container. If you installed using *yum*, just type */etc/init-d/queuemetrics restart*.


* Try and login to QueueMetrics using your browser


* Press on the "Licence" label to see your current active licence, its expiration date and the maximum number of licenced agents.


**See also:**


* [Installing QueueMetrics]({% post_url 2006-12-03-installing-queuemetrics %}) meta-node


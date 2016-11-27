---
layout: post
title:  XML-RPC not working
date:   2006-12-05 16:58:40.0
tags:
 - xmlrpc
 - xerces
 - QueueMetrics
 - faq

categories: update
---

If the XML-RPC interface of QueueMetrics is not working, i.e you get no meaningfiul response, you have to look up the catalina.out file in order to detect possible problems.

**Xerces not installed**

With Java 1.5 and Tomcat 5.5, you may encounter the problem as an error string stating *redstone.xmlrpc.XmlRpcException: Could not instantiate XMLReader parser*. This means the Xerces parser cannot be found.
If this happens, download Xerces from [http://xerces.apache.org/xerces-j/](http://xerces.apache.org/xerces-j/) and put the file called *xercesImpl.jar* in your *queuemetrics/WEB-INF/lib*. This should solve your problems.
(Thanks to Matt B. for input)




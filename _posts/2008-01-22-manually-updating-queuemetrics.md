---
layout: post
title:  Manually updating QueueMetrics
date:   2008-01-22 10:59:31.0
tags:
 - update
 - QueueMetrics
 - manual
 - #frontpage

categories: update
---

This recipe assumes that you have a working QueueMetrics installation and you just need to update it to a newer version.
It should work with any servlet container.


* make a backup copy of the old *queuemetrics* webapp and of the old *queuemetrics* database

* install the new version (just unpack it unto the *webapps/* space)

* copy the *mysql-connector.jar* from the old *WEB-INF/lib* to the new (so it's got the DB connector)

* copy the *WEB-INF/web.xml* file from the old to the new version (so the licence key and the DB connection are preserved)

* restart tomcat

* when you start up QM, it will likely complain of an error with the database version and drive you to the database update tool. Run it. If it doesn't, go to *http://.../queuemetrics/dbtest* anyway and see if that page reports any error.

* things should now work. As a last step, make a diff of the old *WEB-INF/configuration.properties* to the new one, and update properties that were manually changed in the old version.

**See also**


* [Installing QueueMetrics]({% post_url 2006-12-03-installing-queuemetrics %})

* [Updating QueueMetrics using yum]({% post_url 2008-09-10-updating-queuemetrics-using-yum %})

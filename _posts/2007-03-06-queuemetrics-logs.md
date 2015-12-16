---
layout: post
title:  QueueMetrics logs
date:   2007-03-06 17:20:27.0
tags:
 - reporting
 - QueueMetrics
 - logs
 - errors
 - #frontpage

categories: update
---

QueueMetrics does not have its own logging facilities, but uses the logging facilities provided by the servlet container it's running into (that's in most cases Apache Tomcat).

In a standard QueueMetrics installation, they are located in */usr/local/queuemetrics/tomcat/logs* and look a bit like the following example:

    
    [root@qm ~]# cd /usr/local/queuemetrics/tomcat/logs/
    [root@qm logs]# ll
    total 184
    -rw-r--r--  1 root root 53897 Feb 21 21:56 catalina.out
    -rw-r--r--  1 root root  5411 Nov 28 16:54 localhost_log.2006-11-28.txt
    -rw-r--r--  1 root root  4210 Dec  2 23:46 localhost_log.2006-12-02.txt
    -rw-r--r--  1 root root  4207 Dec  8 14:26 localhost_log.2006-12-08.txt
    -rw-r--r--  1 root root 11278 Dec 14 19:01 localhost_log.2006-12-14.txt
    -rw-r--r--  1 root root  7940 Dec 20 13:02 localhost_log.2006-12-20.txt
    -rw-r--r--  1 root root 43429 Jan 18 22:15 localhost_log.2007-01-18.txt
    -rw-r--r--  1 root root  7263 Jan 20 17:52 localhost_log.2007-01-20.txt
    -rw-r--r--  1 root root 25099 Feb 21 21:56 localhost_log.2007-02-21.txt


The logs are comprised of a general file named *catalina.out* and of many subfiles, usually one per day, with the day's activity, usually named *localhost-date.txt*.

If you encounter any errors in QueueMetrics, it may be useful to send Loway the logs while the program was running. As the logs get extremely verbose, in order to report an error you should do the following:

* Stop QueueMetrics by issuing the command */etc/init.d queuemetrics stop*

* Delete (or copy to some other directory) the files named *catalina.out* and *localhost-{date}.txt*

* Restart QueueMetrics by issuing */etc/init.d queuemetrics start*

* Reproduce the problem. Please make sure there is only one active user while you do this, as all user sessions will send data to the same logs.

* Send over the new *catalina.out* and *localhost-{date}.txt* files.

If you report a problem, it will also be helpful to have the following information:

* The QueueMetrics configuration, as expressed by the files *web.xml* and *configuration.properties* under the WEB-INF directory in QueueMetrics. In a standard QueueMetrics install, you can find them under /usr/local/queuemetrics/webapps/queuemetrics-{version}''

* The running queue_log file, as found in */var/log/asterisk/queue_log*

* A screenshot of the QueueMetrics licence page, stating the exact versions of the Java environment used.

* The system information, as obtained by the command *uname -a* in Linux

Do not forget to add the exact local time the error was experienced, so that it's easy to track it.

**Reporting a real-time problem**

If you are exepriencing a problem on the real-time page, it is very useful if you can send over a screenshot where you are seeing:
- The QM realtime page
- A shell window showing "tail -f /var/log/asterisk/queue_log" 

so it's very easy to match what is going on with the last lines of the queue_log.




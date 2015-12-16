---
layout: post
title:  Avoiding queue_log file rotation
date:   2006-10-06 16:43:25.0
tags:
 - queue_log
 - QueueMetrics
 - logrotate
 - #frontpage

categories: update
---

If you run a call center, you will definitely want the log rotation susbsystem not to rotate your *queue_log* file along with the other Asterisk logs found in */var/log/asterisk*. The *queue_log* file contains essential information on how the call-center is going that is being used by software like [QueueMetrics](http://queuemetrics.loway.it) to report on the well-being and the actual work being performed by your CC, and you surely want to keep that data in a safe place for cross-period analysis.
The majority of prebuilt Asterisk distributions will instead lotate that file together with the other Asterisk logs, and this may cause lost data if you do not have a backup.

**Disabling log rotation**

Disabling log rotation is actually quite easy: go to */etc/logrotate.d* and look for a file named *asterisk*. If you run TrixBox, you'll find something like:

    
    /var/log/asterisk/*log {
       missingok
       rotate 5
       weekly
       create 0640 asterisk asterisk
       postrotate
           /usr/sbin/asterisk -rx 'logger reload' > /dev/null 2> /dev/null
       endscript
    }


There may as well be other sections where other files are rotated. You just delete the section above and the queue_log file will not be rotated anymore.

**What if my queue_log has already been rotated?**


Loway offers a script that makes it trivial to do the import without following the manual procedure -  you'll find it here: 

[http://queuemetrics.com/manuals/QLoader-chunked/ar01s02.html](http://queuemetrics.com/manuals/QLoader-chunked/ar01s02.html)

Do an automated install using yum.


**Manual procedure**


If your queue_log has already been rotated, you'll want to join the remaining pieces together. That's very easy:


* First, stop Asterisk. 

* Make a backup of all *queue_log** files

* Rename the existing *queue_log* to *queue_log.now*. 

* Execute the following commands (I give examples for 5 leftover pieces, you may find a different number on your system):

    
    cat queue_log.5 >> queue_log
    cat queue_log.4 >> queue_log
    cat queue_log.3 >> queue_log
    cat queue_log.2 >> queue_log
    cat queue_log.1 >> queue_log
    cat queue_log.now >> queue_log



* Remove all files but queue_log itself

* Restart Asterisk. 

This should be it. Now your QueueMetrics will work just fine.


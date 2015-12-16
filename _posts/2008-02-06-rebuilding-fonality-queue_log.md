---
layout: post
title:  Rebuilding Fonality queue_log
date:   2008-02-06 15:41:24.0
tags:
 - queue_log
 - Fonality

categories: update
---

On a number of Fonality boxes, you'll find that the *queue_log* file, which content is critical to the correct running of [QueueMetrics]({% post_url 2005-10-11-queuemetrics %}), is deleted almost immediately and moved to varius files under the */var/log/asterisk/real* filesystem.
In order to make this work with QueueMetrics, we prepared a small script that will rebuild the queue_log out of the snippets in *real*. In order not to overload the system, we take only s pecified period pof time (eg 30 days) into consideration for the rebuilding.

Here is the script called **rebuildqlog.sh**


    
    # uso:  ./rebuildqlog.sh /var/log/qm_qlog
    # --------------------------------------------------
    
    LOGF=$1
    LOGT=$LOGF.temp
    LOGS=$LOGT.sorted
    
    MAXDAYS=30
    
    rm -f $LOGT $LOGS
    nice find /var/log/asterisk/real -name queue_log.\* -mtime -$MAXDAYS | xargs cat >> $LOGT
    cat /var/log/asterisk/queue_log >> $LOGT
    nice sort --buffer-size=10M $LOGT | uniq > $LOGS
    
    rm -f $LOGT $LOGF
    mv $LOGS $LOGF



Then we need to call this script every 15 seconds or so in order to have a reasonably up-to-date version of the queue_log file ready for use by QueueMetrics.

So we create the file **run_rebuild.sh**
:

    
    #! /bin/bash
    
    while [ true ]
    do
            nice /root/rebuildqlog.sh /var/log/qm_qlog
            sleep 15
    done


And we run everything by typing:

    
    nohup /root/run_rebuild.sh &



As we build the "new" *queue_log* in a different location, we'll have to modify the file *configiration.properties* in QueueMetrics in order to tell it where the queue_log to use actually is:

    
    # This is the default queue log file.
    default.queue_log_file=/var/log/qm_qlog


This should do the trick. 

**Note:**


* You cannot use the new *queue_log* file with qloaderd, as the file is constantly rebuilt.




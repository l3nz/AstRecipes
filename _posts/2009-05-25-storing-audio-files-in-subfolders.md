---
layout: post
title:  Storing audio files in subfolders
date:   2009-05-25 18:07:21.0
tags:
 - monitor
 - daily
 - audio

categories: update
---

It is often useful to store audio recordings in different subfolders, e.g. per day.

We imagine that you want to record queue traffic on an external volume called **/recording**
.



* **Creating subfolders**


It is quite useful to create subfolders in advance, so you do not have to create them directly from the Asterisk dialplan.

You can create them all using a simple script like the one here:

    
    
    for YYYY in 2009 2010 2011 2012
    do
    
    for MM in 01 02 03 04 05 06 07 08 09 10 11 12
    do
    
    for DD in 01 02 03 04 05 06 07 08 09 10 \
              11 12 13 14 15 16 17 18 19 20 \
              21 22 23 24 25 26 27 28 29 30 31
    do
            mkdir -p $YYYY/$MM/$DD
    done
    
    echo Created: $YYYY/$MM
    
    done
    done


You should then make sure that all the new directories are owned by the same process that is running Asterisk, e.g.

    
    chown -R asterisk.asterisk /recordings


If you run Asterisk as "asterisk".

**Note**
: In any case, most Asterisk versions will create the folders when needed, as long as the parent directory is owned by the Asterisk process. 
>>Thanks Atis for [pointing it out](http://lists.digium.com/pipermail/asterisk-users/2009-May/232394.html)!<<


* **Asterisk dial-plan**


In your extensions.conf, you can set up a queue to be recorded like in the following example:

    
    [queue-abc]
    exten => s,1,Wait(3)
    exten => s,n,Answer
    exten => s,n,Set(MONITOR_FILENAME=/recordings/${STRFTIME(${EPOCH},,%G/%m/%d)}/queue_abc-${UNIQUEID}.WAV)
    exten => s,n,Queue(queue_abc|t|||150)


This will create filenames like */recordings/2009/05/25/queue_abc-1243267147.301.WAV* that can be used for efficient storage.

**See also**


* [Listening to multi-folder recordings in QueueMetrics]({% post_url 2009-05-25-listening-to-multi-folder-recordings-in-queuemetrics %})





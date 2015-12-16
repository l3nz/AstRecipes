---
layout: post
title:  Configuring a queue
date:   2005-04-25 15:04:10.0
tags:
 - queue
 - monitor
 - #popular

categories: update
---

The following queue works as following:


* all calls are monitored, i.e. saved to disk 

* if after 60 seconds on the queue the call is unanswered, the call is routed to voicemail 

* there are two levels of agents: agents 302 and 303 will answer the queue (level 1); only if none of them is available the call is routed to agent 301 (level 2). If nobody is available, the queue keeps trying until timeout is reached. 

**Extensions.conf**


    
    [q-my-sample]
    ; ...queue description.....
    exten =>
    s,1,SetVar(MONITOR_FILENAME=/var/spool/asterisk/queuecalls/QSAMPLE-${
    UNIQUEID})
    exten => s,2,Queue(q-sample|n|||)
    exten => s,3,Playback(voicemail-invitation)
    exten => s,4,VoiceMail,s2001


**Queues.conf**


    
    [q-sample]
    music = default
    announce = q-sample-announce
    strategy = roundrobin
    timeout = 60
    retry = 5
    maxlen = 0
    announce-frequency = 0
    announce-holdtime = no
    monitor-format = wav
    monitor-join = yes
    queue-youarenext = silence
    queue-thankyou = q-sample-thankyou
    member=>Agent/302,0
    member=>Agent/303,0
    member=>Agent/301,1



More information on configuring a queue can be found at [Understanding queue logic]({% post_url 2006-03-12-understanding-queue-logic %}).


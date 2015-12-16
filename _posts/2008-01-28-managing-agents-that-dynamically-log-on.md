---
layout: post
title:  Managing agents that dynamically log-on
date:   2008-01-28 09:25:07.0
tags:
 - queue_log
 - QueueMetrics
 - agents
 - AddQueueMember
 - #frontpage

categories: update
---

AddQueueMember is a command that lets you add dynamic agents to a queue. Its main advantage is that you can add channels, i.e. terminals, so you'll have most of the advantages of agents without the performance and stability problems that the agents module may cost in very large systems.

Its disadvantage is that in Asterisk 1.2 it does not log the agent login/logoff to the queue_log, and so programs that analyze the queue log data like [QueueMetrics]({% post_url 2005-10-11-queuemetrics %}) will not see agents logging on and off. This is a major organizational problem in a real-world call center, where tracking agent logons and logoffs is vital to the smooth running of the operations. In Asterisk 1.4 instead this bug has been fixed, so you can use this code:

**Code for Asterisk 1.4 and QueueMetrics 1.4 or newer**


To do the adding, you dial 422XX, where XX is your local extension;  the same goes to be removed from queue.

    
    ; addqueuemember - 422 - for Asterisk 1.4
    exten => _422XX,1,Answer
    exten => _422XX,2,AddQueueMember(my-queue,SIP/${EXTEN:3})
    exten => _422XX,3,Hangup


    
    ; removequeuemember - 423 - for Asterisk 1.4
    exten => _423XX,1,Answer
    exten => _423XX,2,RemoveQueueMember(my-queue,SIP/${EXTEN:3})
    exten => _423XX,3,Hangup



**Code for Asterisk 1.2 and QueueMetrics 1.4 or newer**


In case you run Asterisk 1.2, you'll have to implement this piece of dialplan instead:


    
    ; addqueuemember - 422
    exten => _422XX,1,Answer
    exten => _422XX,2,AddQueueMember(my-queue,SIP/${EXTEN:3})
    exten => _422XX,3,System( echo  
    "${EPOCH}|${UNIQUEID}|my-queue|SIP/${EXTEN:3}|ADDMEMBER|-" >> /var/log/asterisk/queue_log )
    exten => _422XX,4,DBput(dynlogin/log_Agent-${EXTEN:3}=${EPOCH})
    exten => _422XX,5,Hangup


    
    ; removequeuemember - 423
    exten => _423XX,1,Answer
    exten => _423XX,2,RemoveQueueMember(my-queue,SIP/${EXTEN:3})
    exten => _423XX,3,DBget(ORGEPOCH=dynlogin/log_Agent-${EXTEN:3})
    exten => _423XX,4,Set(RV=$[${EPOCH} - ${ORGEPOCH}])
    exten => _423XX,5,GotoIf($["${RV}" = "0"]?8:6)
    exten => _423XX,6,System( echo  "${EPOCH}|${UNIQUEID}|my-queue|SIP/${EXTEN:3}|REMOVEMEMBER|-|${RV}" >> /var/log/asterisk/queue_log )
    exten => _423XX,7,DBdel(dynlogin/log_Agent-${EXTEN:3})
    exten => _423XX,8,Hangup


This will allow logging in one queue at a time and QueueMetrics correctly tracking that agent being associated with that queue and computing correct overall agent activity statistics.

**See also:**


* [AddQueueMember and the queue_log file]({% post_url 2005-10-11-addqueuemember-and-the-queue_log-file %}) is an outdated article that deals with older versions of QueueMetrics

* [Using fully dynamic members with QM 1.4 and Asterisk 1.2](http://queuemetrics.com/faq.jsp#faq-039-dynamic_members_12) is a different example showing the same technique applied here.

* [Using fully dynamic members with QM 1.4 and Asterisk 1.4](http://queuemetrics.com/faq.jsp#faq-040-dynamic_members_14) is a different example showing the same technique applied here.




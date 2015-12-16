---
layout: post
title:  AddQueueMember and the queue_log file
date:   2005-10-11 10:15:55.0
tags:
 - queue_log
 - QueueMetrics
 - agents
 - AddQueueMember

categories: update
---


* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
*
*
**THIS NODE IS OUTDATED - IF YOU RUN QUEUEMETRICS 1.4 OR NEWER, SEE [Managing agents that dynamically log-on]({% post_url 2008-01-28-managing-agents-that-dynamically-log-on %})**


* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
* 
*
*



AddQueueMember is a command that lets you add dynamic agents to a queue. Its main advantage is that you can add channels, i.e. terminals, so you'll have most of the advantages of agents without the performance and stability problems that the agents module may cost in very large systems.

Its disadvantage is that it does not log the agent login/logoff to the queue_log, and so programs that analyze the queue log data like [QueueMetrics]({% post_url 2005-10-11-queuemetrics %}) will not see agents logging on and off. This is a major organizational problem in a real-world call center, where tracking agent logons and logoffs is vital to the smooth running of the operations.

The answer - bad as it may seem - is to add a fake queue_log data for each logon and logoff. This was covered by  
[http://lists.digium.com/pipermail/asterisk-dev/2005-February/009615.html](http://lists.digium.com/pipermail/asterisk-dev/2005-February/009615.html)

Unfortunately this solution is not so simple, because:
1. when an agent logs off, they log how much time they have been on line
2. multiple logon/logoff lines are *bad* for log analysis

This recipe presents a simple logon-logoff script that might help you in "faking it". Of course you should likely add authentication for a real-world usage. 

To do the adding, you dial 422XX, where XX is your local extension;  the same goes to be removed from queue.

    
    ; addqueuemember - 422
    exten => _422XX,1,Answer
    exten => _422XX,2,AddQueueMember(my-queue,SIP/${EXTEN:3})
    exten => _422XX,3,System( echo  
    "${EPOCH}|${UNIQUEID}|NONE|SIP/${EXTEN:3}|AGENTLOGIN|-" >> /var/log/asterisk/queue_log )
    exten => _422XX,4,DBput(dynlogin/log_Agent-${EXTEN:3}=${EPOCH})
    exten => _422XX,5,Hangup


    
    ; removequeuemember - 423
    exten => _423XX,1,Answer
    exten => _423XX,2,RemoveQueueMember(my-queue,SIP/${EXTEN:3})
    exten => _423XX,3,DBget(ORGEPOCH=dynlogin/log_Agent-${EXTEN:3})
    exten => _423XX,4,Set(RV=$[${EPOCH} - ${ORGEPOCH}])
    exten => _423XX,5,GotoIf($["${RV}" = "0"]?8:6)
    exten => _423XX,6,System( echo  "${EPOCH}|${UNIQUEID}|NONE|SIP/${EXTEN:3}|AGENTLOGOFF|-|${RV}" >> /var/log/asterisk/queue_log )
    exten => _423XX,7,DBdel(dynlogin/log_Agent-${EXTEN:3})
    exten => _423XX,8,Hangup


With this setup, we verified that the queue_log can be  analyzed by QueueMetrics and the dynamic agent shows up fine (albeit with the name of a terminal, like SIP/23, instead of the usual Agent/23 string, but you can modify it in QM itself).

This setup might even be used in a call center where agents are not  actually used but queues connect straight to terminals to "fake" agent  logon/logoff, in order to have such data available for reporting.


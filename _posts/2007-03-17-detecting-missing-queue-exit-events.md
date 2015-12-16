---
layout: post
title:  Detecting missing Queue exit events
date:   2007-03-17 16:05:29.0
tags:
 - queue_log
 - queuestatus
 - queue
 - #frontpage

categories: update
---


On some version of Asterisk, there are conditions when an unanswered call would exit the queue without leaving a trace of its exiting in the queue_log, therefore generating incomplete call processing events that lead reporting software like [QueueMetrics]({% post_url 2005-10-11-queuemetrics %}) to produduce inaccurate results.

You will notice this if you encounter pieces of the queue_log that look like the following example:

    
    1167421685|1169421668.34679|qname|NONE|ENTERQUEUE||5555551234
    1167099900|1169099887.31480|qname|NONE|ENTERQUEUE||5555551234
    1168222540|1168222529.5949|qname|NONE|ENTERQUEUE||5555551234


Here you can see that the caller-id 5555551234 entered the queue three times in a row but its exiting statuses were never recorded. 

To solve this problem, it is important to know why the queue was exited without response, and this can be obtained with a sample piece of the dialplan like the following:

    
    exten => s,1,queue(myqueue)
    exten => s,2,System( echo "${EPOCH}|${UNIQUEID}|myqueue|NONE|EXITCODE|${QUEUESTATUS}" >> /var/log/asterisk/queue_log )
    exten => h,1,System( echo "${EPOCH}|${UNIQUEID}|myqueue|NONE|EXITHANGUP|${QUEUESTATUS}" >> /var/log/asterisk/queue_log )


You should leave this code running for a while and then review the queue_log manually for traces of ENTERQUEUEs followed only by EXITCODE and EXITHANGUP records. They will be followed by the condition causing the problem, like TIMEOUT, FULL, JOINEMPTY, LEAVEEMPTY, JOINUNAVAIL, or LEAVEUNAVAIL.

After you have this information, you can know how to modify the dialplan to record a fake queue end entry, therefore producing a valid queue_log file.

Note: version 1.4 of Asterisk should finally be unaffected by this problem.

**See also:**


* [Non existent calls taken when no agents were available seem to be lagging in the queue](http://queuemetrics.com/faq.jsp#faq-019-end-of-queue)


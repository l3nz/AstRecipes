---
layout: post
title:  Removing unclosed entries from the queue_log
date:   2007-11-07 12:47:45.0
tags:
 - QueueMetrics
 - queue_log

categories: update
---

To find all single-event calls in your queue_log you can run the following query:

    
    SELECT call_id, count( * ) , verb
    FROM queue_log
    WHERE partition = 'P001'
    AND verb NOT IN (
    'AGENTCALLBACKLOGIN', 'AGENTCALLBACKLOGOFF', 
    'AGENTLOGIN', 'AGENTLOGOFF', 
    'ADDMEMBER', 'REMOVEMEMBER', 
    'PAUSEREASON', 'CALLSTATUS'
    )
    GROUP BY call_id
    HAVING count( * ) = 1 


Change the partition as you see fit.
Then you should delete them by call-id.






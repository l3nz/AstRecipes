---
layout: post
title:  Recording queue transfers to disk
date:   2006-03-13 09:33:26.0
tags:
 - queue
 - monitor
 - #frontpage

categories: update
---

Recording a call being handled by a queue is trivial - you just set the file name and the monitor format, and there you are.

The problem starts when you want the transfers from the queue system to be recorded as well. If you use the "Transfer" button on your telephone the agent won't be disconnected from the call as long as Asterisk is concerned, so he won't be offered any more calls while the original call lasts. This is a general issue with Asterisk queues, and the solution is to start an unattended transfer by pressing the "#" key followed by the extension of the  person to be sent the call. 

If you behave like this, the call will be recorded as long as you enter the DTMF digits, and will then be dropped. This is not okay for us, as we would like to keep monitoring the call even after the transfer is complete.

We would also like to record the second part of the call with a meaningful file name, one that might make it very easy to match the first to the second part of the call.

**Solution**

First set up a standard queue in extensions.conf, like this:

    
    [coda-dps]
    exten => s,1,Wait(3)
    exten => s,n,Answer
    exten => s,n,Set(__MONITOR_FILENAME=DPS-${UNIQUEID})
    exten => s,n,Set(__TRANSFER_CONTEXT=queuetransfer)
    exten => s,n,Queue(queue-dps|t|||150)
    exten => s,n,VoiceMail,90


This is a very basic queue setup. I imagine you already know how to create a queue in *queues.conf* (if you don't, see the node [Configuring a queue]({% post_url 2005-04-25-configuring-a-queue %}) here on AstRecipes).

What matters is that:
1. we define the file name MONITOR_FILENAME as an inheritable variable
2. we decide in which context shall the extension typed in be searched
3. we tell the queue app to allow agents' transfers (the 't' option).

We will then create a new context *queuetransfer* to allow for the transfer to take place:
    
    [queuetransfer]
    exten => _XX,1,Answer
    exten => _XX,2,Monitor(wav,${MONITOR_FILENAME}_TR,m)
    exten => _XX,3,Dial(SIP/${EXTEN})

This way we inherit the name of the original file we were monitoring to, and we create another one with the same name followed by "_TR".





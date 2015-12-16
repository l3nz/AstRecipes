---
layout: post
title:  Recording all outgoing traffic for some extensions
date:   2007-08-08 16:56:21.0
tags:
 - recording
 - monitor
 - extension
 - #frontpage

categories: update
---

You want to record all outgoing traffic done by some extensions - not all extensions (it would be trivial) but just some of your choosing. You also want to be able to turn recordings on or off without modifying the dialplan.

**How to do it**

The idea is simple: we use Asterisk's internal database to record a flag for each extension that tells us whether to record those calls or not. We add a check for this flag before dialling out, so we can catch all outgoing traffic. 

**Modifying the dialplan**

Whenever you are currently dialing out (we assume that any number starting with 0 is an external call) you add the following piece of dialplan:

    
    exten => _0.,1,NoOP,Dial out with hidden CLID
    exten => _0.,2,SetCallerPres(prohib)
    exten => _0.,3,DBGet(rec=registra/${CALLERIDNUM})
    exten => _0.,4,GotoIf($[ ${rec} = 1 ]?10:20)
    exten => _0.,10,MixMonitor(REC-${CALLERIDNUM}-${UNIQUEID}.wav|b|)
    exten => _0.,11,Goto(20)
    exten => _0.,20,Dial(Zap/g1/${EXTEN:1})



**Deciding which extensions will be recorded**

Once your piece of dialplan is in place, you use the following command from the Asterisk CLI to tell Asterisk an extension is to be recorded:

    
    database put registra 299 1


While you use the following to tell Asterisk an extension is not to be recorded anymore:

    
    database put registra 299 0


If you want to know the status of your recorded extension, you type:

    
    ast*CLI> database show registra
    /registra/223                                     : 1
    /registra/224                                     : 0
    /registra/299                                     : 1


Any key that has value of 1 will be recorded; any other key (whether it has value 0 or does not exist) is not recorded.

**Finding recorded calls**

You can find the recorded calls by issuing the following command:

    
    [root@ast monitor]# ls -l /var/spool/asterisk/monitor/REC*
    -rw-r--r--  1 root root  186284 Aug  8 16:36 /var/spool/asterisk/monitor/REC-299-1186583777.73726.wav
    -rw-r--r--  1 root root  206764 Aug  8 16:40 /var/spool/asterisk/monitor/REC-299-1186584038.73774.wav


And you can listen to them by copying them over to any audio software.







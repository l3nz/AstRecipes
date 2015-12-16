---
layout: post
title:  Connecting to Squillo using SIP
date:   2005-05-05 17:54:34.0
tags:
 - Squillo
 - SIP
 - connecting
 - #frontpage

categories: update
---

Squillo.it - see [http://www.squillo.it](http://www.squillo.it) - is an Italian VoIP company offering geographical termination in SIP.

To connect your Asterisk box to Squillo, you should edit the following files:

**-- sip.conf --**

    
    [global]
    ....
    canreinvite=no
    register => **USER**:**PASS**@sip.squillo.it
    
    [squillo]
    type=friend
    username=**USER**
    fromuser=**USER**
    secret=**PASS**
    host=sip.squillo.it
    context=from-squillo
    insecure=very
    disallow=all
    allow=ilbc
    allow=gsm
    allow=ulaw
    allow=alaw
    dtmfmode=rfc2833


This way you register to Squillo as if your * box was a simple telephone. Incoming calls are routed to the context *from-squillo*.

**-- extensions.conf --**

    
    [default]
    exten => _0.,1,Dial(SIP/${EXTEN:1}@squillo,30,tr)
    
    [from-squillo]
    exten => s,1,Dial(SIP/501,30,tr)


This example shows how to place and receive calls from Squillo (in this example, all incoming calls are routed to the single telephone SIP/501).


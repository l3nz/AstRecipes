---
layout: post
title:  Connecting to FWD using IAX
date:   2005-04-25 15:05:25.0
tags:
 - IAX
 - FreeWorldDialup
 - #popular
 - connecting

categories: update
---

It is possible to connect an Asterisk box to FreeWorldDialup using either SIP or IAX2.

This article assumes you already have a working FWD account; if you don't, you can easily create it right on the site.

The following strings must be substituted in the examples: 

* *LOGIN* is your FWD number 

* *PASS* is your FWD password 

* *INCOMINGFWD* is a context created for incoming FWD calls (you can leave it as INCOMINGFWD) 

* *SIP/myphone* is a telephone terminal to ring on incoming FWD calls 

To use IAX, you have to: 

* Visit [http://account.freeworlddialup.com/index_new.php?section_id=112](http://account.freeworlddialup.com/index_new.php?section_id=112) to activate your account for IAX 


* Edit the file /etc/asterisk/iax.conf in your Asterisk box, adding: 
    register => LOGIN:PASS@iax.fwdnet.net

in the <[>General<](-NOTFOUND->General<)> section (this part enables outgoing calls from you to FWD) 
In the same file, add by the end of the file: 

    
    [iaxfwd]
    type=user
    context=INCOMINGFWD
    auth=rsa
    inkeys=freeworlddialup

(This part enables incoming calls from FWD to you). 


* In /etc/asterisk/extensions.conf, create a context for INCOMING calls, i.e. calls coming from FWD. 
    
    [INCOMINGFWD]
    exten => LOGIN,1,Dial(SIP/myphone,20,tr)
    exten => LOGIN,2,Voicemail(33)
    exten => LOGIN,102,Voicemail(33) 

Of course, you can dial any other telephone terminal or do whatever you want. In this case I ring a terminal for 20 seconds and then start voicemail. 


*In /etc/asterisk/extensions.conf, edit your user context to allow calling FWD: 
    
    exten => _1#.,1,SetCallerId,"LOGIN"
    exten => _1#.,2,Dial(IAX2/LOGIN:PASS@iax2.fwdnet.net/${EXTEN:2},60,r)
    exten => _1#.,3,Congestion 

This way, when I want to dial a FWD number, I start it with 1#, like by typing 1#123456 I call FWD 123456.

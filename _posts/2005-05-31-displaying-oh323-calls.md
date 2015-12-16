---
layout: post
title:  Displaying OH323 calls
date:   2005-05-31 10:27:33.0
tags:
 - OH323
 - #frontpage

categories: update
---

This node is meant as a handy guide to showing ongoing OH323 calls in your Asterisk system. 

To make full use of this tutorial, you first have to start the Asterisk CLI, with a command like:

    
    asterisk -vvvvvvvvvvvvvvvvvvr


It will attach itself to the local Asterisk instance.

The basic OH323 monitoring commands are all under *oh323 show ...*

**oh323 show estabilished**

This is the basic command showing how may active connections are present in the system. Nothing to be too excited about. :-) the first line in the example is the line you enter.

    
    ast*CLI> oh323 show established
    2 established H.323 connection(s)


**oh323 show info**

This command exposes the details of the ongoing connections. Note how you can see the codec and the call state. (Note that from oh323 v0.6.5 onwards, the command is called **oh323 show channels**
).

    
    ast*CLI> oh323 show info
    Information about active OpenH323 channel(s)
    --------------------------------------------
     Num. Token                          State   Init      RX/TX   Format       Remote RTP Addr.      Local RTP Addr.
        0 ip$10.1.2.215:14636/11346      ESTABLI Remote   960/320  ALAW         10.1.2.215:18394      10.1.2.201:12158
        1 ip$10.1.2.215:14979/11380      ESTABLI Remote   960/320  ALAW         10.1.2.215:18430      10.1.2.201:12162
        2 ip$localhost/21368             RING    Local      0/0    UNKN
        3 ip$localhost/21364             ESTABLI Local    960/320  ALAW         10.1.2.120:20706      10.1.2.201:12160
        4 ip$localhost/21369             RING    Local      0/0    UNKN
        5 ip$localhost/21370             RING    Local      0/0    UNKN
    
      [3 established H.323 connection(s)]
      [In-call rate: 0.00 CPS]
      [In-call block ratio: 0 %]


**oh323 show vars**

This command shows the gory details of each call, as seen by the channel variables that can be accessed by the Asterisk dial plan. Note howw you can therefore get the caller id and the used codec.

    
    ast*CLI> oh323 show vars
    Variables for OpenH323 channel OH323/R11380:
            OH323_CTOKEN    'ip$10.1.2.215:14979/11380'
            OH323_SRCALIAS  '06xxxxxxxxx [10.1.2.215]'
            OH323_DSTALIAS  '9924   9924    ip$10.1.2.201:1720'
            OH323_SRCE164   '06xxxxxxxxx'
            OH323_DSTE164   '9924'
            OH323_REMOTEAPP 'innovaphone IP3000     V5.01 sr1 [04-5806]     4/129'
            OH323_CHANCODEC 'ALAW'
    Variables for OpenH323 channel OH323/L21373:
            OH323_CTOKEN    'ip$localhost/21373'
            OH323_SRCALIAS  '06xxxxxxxxx  (9910, 9911, 9912, 9913, 9914, 9915, 9916, 9917, 9922, 9923, 9924, 9925) [10.1.2.201]'
            OH323_DSTALIAS  '811    811     ip$10.1.2.215:1720'
            OH323_SRCE164   '06xxxxxxxxx'
            OH323_DSTE164   '811'
            OH323_REMOTEAPP 'innovaphone IP3000     V5.01 sr1 [04-5806]     4/129'
            OH323_CHANCODEC 'ALAW'



**oh323 show stats**

This command gives more general aggregate stats about the channel driver, since it was last started:
    
    ast*CLI> oh323 show stats
    Statistics of OpenH323 channel driver
    -------------------------------------
    Up since: Fri May 20 14:29:09 2005
    Inbound H.323 calls: 616
    Outbound H.323 calls: 458
    Dropped inbound H.323 calls: 0
    Blocked outbound H.323 calls: 0
    Total inbound H.323 calls detected: 627
    Total outbound H.323 calls attempted: 2773
    H.323 call errors: 0
    H.323 answer errors: 0



**oh323 show conf**

This command shows the information set in /etc/asterisk/oh323.conf
    
    ast*CLI> oh323 show conf
    Configuration of OpenH323 channel driver
    ----------------------------------------
    Version: 0.6.2
    Listening on address: 0.0.0.0:1720
    Gatekeeper used: No Gatekeeper
    FastStart/H245Tunnelling/H245inSetup: OFF/OFF/OFF
    Supported formats in pref. order: ALAW<0>
    Jitter buffer limits (min/max): 20-100 ms
    TCP port range: 10000 - 20000
    UDP (RAS) port range: 10000 - 20000
    UDP (RTP) port range: 10000 - 20000
    IP Type-of-Service value: 0
    User input mode: 2
    Max number of inbound H.323 calls: 20
    Max number of outbound H.323 calls: 20
    Max number of simultaneous H.323 calls: 40



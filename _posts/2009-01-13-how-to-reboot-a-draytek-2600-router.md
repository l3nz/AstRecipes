---
layout: post
title:  How to reboot a Draytek 2600 router
date:   2009-01-13 14:16:00.0
tags:
 - Vigor
 - router
 - reboot
 - DrayTek
 - 2600

categories: update
---

This recipe explains how to reboot a Draytek Vigor 2600 router from the Unix command line. 

Simply run the following command to reboot router 192.168.5.1 whith administrative user USER password PASS:

    
    wget -O - "http://USER:PASS@192.168.5.1/cgi-bin/reboot.cgi?sReboot=Current&submit=++OK++"


You get a message like:

    
    --14:17:50--  http://USER:*password*@192.168.5.1/cgi-bin/reboot.cgi?sReboot=Current&submit=++OK++
               => `-'
    Connecting to 192.168.5.1:80... connected.
    HTTP request sent, awaiting response... 302 Found
    Location: http://192.168.5.1/doc/reboot1.sht [following]
    --14:17:51--  http://192.168.5.1/doc/reboot1.sht
               => `-'
    Connecting to 192.168.5.1:80... connected.
    HTTP request sent, awaiting response... 401 Unauthorized
    Authorization failed.


and the router reboots immediately.


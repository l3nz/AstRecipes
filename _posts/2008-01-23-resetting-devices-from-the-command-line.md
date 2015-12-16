---
layout: post
title:  Resetting devices from the command line
date:   2008-01-23 11:45:33.0
tags:
 - reset
 - Vigor
 - DrayTek

categories: update
---

It is sometimes handy to be able to reset network and telephony apparatus right from the command line, in order to script them in case that is needed.

We use:

* server as the IP address of the server

* login  as the login (auth) name

* pass   as the password


**DrayTek Vigor 2600 series routers**

You just issue:

    
    
    wget http://login:pass@server/cgi-bin/reboot.cgi?sReboot=Current&submit=++OK++





---
layout: post
title:  Installing the Welltech LP302 telephone
date:   2005-05-14 13:27:02.0
tags:
 - welltech
 - telephone
 - lp302
 - #frontpage

categories: update
---

This recipe will help installing a Welltech Lanphone LP302 telephone to work with your Asterisk box.

In this example we will imagine that the LP302 will be on a local network with your Asterisk box and the Asterisk box will take care of all communication with the rest of the world. We imagine not to have any bandwidth problem between Asterisk and the LP302.
We also imagine that the LP302 will have a DHCP assigned address, while the Asterisk server will have a static IP address.

Right after the boot (that usually takes quite a while), the LP302 will try and obtain a DHCP address. To know the address of your phone, press "Menu" then use the down arrow to find the network configuration and press "OK". The big display is really helpful.

Open your browser and go to the obtained IP address. The default login is "root" without a password.

As a basic setup, enter the following data:
    
    DHCP: on
    SNTP: enable
    SNTP server: 168.95.195.12
    Time format: 24
    GMT: 2

(Adjust GMT accordingly to where you are - this is for CET).

As a SIP setup, enter the following data:
    
    Run mode: proxy
    Prefix: null
    Line number: 711
    Line account: 711
    Line password: ***


Edit your line number accordingly (keep it the same as your line account) and the password you like best.

Reboot your telephone.

Now edit Asterisk's /etc/asterisk/sip.conf and add the following section:

    
    [711]
    type=friend
    secret=***
    callerid="My LP302" <711>
    host=dynamic
    canreinvite=no
    disallow=all
    allow=alaw
    allow=ulaw
    context=sip
    dtmfmode=inband
    outgoinglimit=1


Calls from the LP302 will be processed in the context 'sip'.

Wait for the telephone to register and try it.

With this same configuration, transfers work and so does music on hold and DTMF tones.

**Links:**


* [http://www.welltech.com.tw/product_e_05.htm](http://www.welltech.com.tw/product_e_05.htm)









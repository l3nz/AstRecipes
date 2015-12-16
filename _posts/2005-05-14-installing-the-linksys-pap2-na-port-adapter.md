---
layout: post
title:  Installing the Linksys PAP2-NA port adapter
date:   2005-05-14 13:40:42.0
tags:
 - telephone
 - PAP2-NA
 - Linksys
 - #frontpage

categories: update
---

This recipe will help installing a Linksys PAP2 port adapter to work with your Asterisk box.

In this example we will imagine that the PAP2 will be on a local network with your Asterisk box and the Asterisk box will take care of all communication with the rest of the world. We imagine not to have any bandwidth problem between Asterisk and the PAP2.
We also imagine that the PAP2 will have a DHCP assigned address, while the Asterisk server will have a static IP address.

The Linksys does not start in DHCP client mode, so you have to activate it manually. 


* Connect an analog phone to the line "phone1"

* Enter **** on the keyboard (4 stars)

* Key in the sequence 101#1#1  to turn DHCP client on

* Key in 110#  to have the unit say its IP address

Open your browser and go tho the said IP address. As a default there is no password to access the unit.


* Select "Admin login" and set "Advanced view"

* Go to "Line 1"

Set the following parameters:
    
    Line enable: yes
    Proxy: 10.10.3.5
    
    Subscriber:
    Display name e User Id: 712
    Password: ***
    Use Auth ID: no
    Preferred codec: G711a


Set it the same way for line 2, using a different account (713 in our case). Note that the SIP ports will be different; that's okay.

Then go to:

* Regional -> Hook Flash timer min: .05  (needed for European phones so that the flash hook works)

* Regional -> Interdigit Long Timer: 5 (seconds before the unit starts dialing after the last entered digit)

Reboot your unit.

Notice that the line LEDs will stay ON when the line is registered and be flashing while the line is in use.

Enter the following parameters in your /etc/asterisk/sip.conf to configure both lines:
    
    [712]
    type=friend
    secret=***
    callerid="My Linksys PAP2-NA p1" <712>
    host=dynamic
    canreinvite=no
    disallow=all
    allow=alaw
    allow=ulaw
    context=sip
    dtmfmode=rfc2833
    outgoinglimit=1
    ;incominglimit=1
    
    [713]
    type=friend
    secret=***
    callerid="My Linksys PAP2-NA p2" <713>
    host=dynamic
    canreinvite=no
    disallow=all
    allow=alaw
    allow=ulaw
    context=sip
    dtmfmode=rfc2833
    outgoinglimit=1
    ;incominglimit=1





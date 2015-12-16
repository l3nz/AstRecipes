---
layout: post
title:  Using a HT-488 with Asterisk
date:   2008-01-20 21:50:34.0
tags:
 - SIP
 - HT-488
 - HandyTone
 - Grandstream
 - FXS
 - FXO
 - configuration
 - ATA
 - Asterisk
 - #frontpage

categories: update
---

The Grandstream HandyTone HT-488 is a low-cost SIP analog adapter that offers one FXS port and one FXO port to connect one analog telephone and one POTS line to the unit.

**The scenario**

We want to install the unit with an Asterisk server whick IP is 10.10.3.5 and want to register the telephone attached to the unit as SIP/32, while the FXO line will be seen by Asterisk as SIP/33.

**Installing the HT-488**

As it ships, access for the web interface is disabled for users accessing from the WAN. To enable it, connect the LAN port of the unit to a PC with a cross-cable patch, use the unit's own DHCP server to assign address to the PC and logon by navigating to **http://192.168.2.1**
 (password **admin**
) - go to *Basic settings* and set *WAN side http access*. Save and reboot. 

Connect the unit to your LAN through the WAN port and connect to it through its DHCP-assigned address (to see what that is, connect a phone to the FXS port and digit *****02**
 to have the unit read out the assigned IP address). If the unit did not get an IP address, select the option *****01**
 and then **9**
 to switch from fixed-ip to DHCP mode.

**Firmware upgrade**

It is VERY IMPORTANT that you set the unit with a modern firmware, or you'll have a number of problems. Luckily this is very easy: check the current TFTP server address on Grandsteram site (see below) and enter it under the configuration page, and set "Yes, check for upgrade every 1 minute". Reboot. 
The unit light should go red while the unit downloads and installs the new firmware, it will take like 5 minutes to update.

**Unit configuration**


* Basic settings
Under *NAT/DHCP Server Information & Configuration*

    
    WAN side http access: yes
    Reply to ICMP on WAN port: yes
    Number of Rings: 0
    Forward to VoIP: s
    FXO One Stage Dialing: yes


The Forward to Voip option must be set to "s" in order to have incoming FXO calls sent over to the Asterisk server.


* FXS PORT
    
    SIP Server: 10.10.3.5
    Outbound Proxy: 10.10.3.5
    SIP User ID: 32
    Authenticate ID: 32
    Authenticate Password: XXX
    Name: (leave blank)
    
    SIP Registration: yes
    Unregister On Reboot: yes
    
    Send DTMF: 	via RTP (RFC2833) 
    Send Flash Event: Yes
    Enable Call Features: 	No
    NAT Traversal (STUN): 	 No
    No Key Entry Timeout: 4
    
    Preferred Vocoder: PCMU / PCMA
    Silence Suppression: No
    Fax Mode: Pass-through



* FXO PORT
    
    SIP Server: 10.10.3.5
    Outbound Proxy: 10.10.3.5
    SIP User ID: 33
    Authenticate ID: 33
    Authenticate Password: XXX
    Name: (leave blank)
    
    SIP Registration: yes
    Unregister On Reboot: yes
    
    Send DTMF: 	via RTP (RFC2833) 
    Send Flash Event: No
    NAT Traversal (STUN): No
    
    Preferred Vocoder: PCMU / PCMA
    
    Silence Suppression: 	No
    Fax-mode: Pass-through
    
    PSTN AC Termination: 	270 Ohm
    Enable PSTN Disconnect Tone Detection: yes
    PSTN Silence Timeout: 20
    Enable Current Disconnect: Yes


Save and reboot

**Asterisk configuration**


In SIP.conf enter the following piece of code:

    
    ; FXS port
    [32]
    type=friend
    secret=XXX
    callerid="My FXS" <32>
    host=dynamic
    nat=no
    canreinvite=no
    disallow=all
    allow=ulaw
    context=sip
    qualify=yes
    dtmfmode=rfc2833
    
    ;  FXO port
    [33]
    type=friend
    secret=XXX
    callerid="My FXO" <33>
    host=dynamic
    nat=no
    canreinvite=no
    disallow=all
    allow=ulaw
    context=in-sip
    qualify=yes
    dtmfmode=rfc2833


Once the phones are registered, you have a standard SIP phone as SIP/32 that will send calls to the context *sip*, while the SIP/33 POTS line can be dialled as *Dial(SIP/33/number,30)* and will send incoming calls to *s@in-sip*.

To check that the lines are working, log in to Asterisk and issue:

    
    pbx*CLI> sip show peers
    Name/username              Host            Dyn Nat ACL Port     Status
    33/33                      10.10.3.103      D          5062     OK (5 ms)
    32/32                      10.10.3.103      D          5060     OK (5 ms)



**See also:**


* [Grandstream web site](http://www.grandstream.com/)

* [Grandstream firmware check](http://www.grandstream.com/firmware.html)

* [HT-488 page](http://www.voip-info.org/wiki/view/Grandstream+Handytone-488) on Voip-info.org



---
layout: post
title:  VoiceBlue with Asterisk IP PBX - How to
date:   2007-02-22 14:30:18.0
tags:
 - VoiceBlue
 - Asterisk
 - GSM
 - gateway
 - 2N

categories: update
---

The present document is a step-by-step guide for configuring the 2N VoiceBlue gateway and Asterisk software IP PBX. It includes essential steps for interconnecting the two products rather than a detailed configuration analysis of the two units. For more information on the 2N VoiceBlue gateway configuration refer to the manual placed at http://www.2n.cz/products/gsm_gateways/voip/voiceblue.html. 

**What is 2N VoiceBlue?**

The 2N VoiceBlue GSM gateway is an ideal complementary product to any SIP-based IP PBX. It's suitable for small and medium companies with IP infrastructure and for companies with international affiliates. With VoiceBlue you gain significant savings on outgoing and incoming calls from IP to GSM networks and backwards. Thanks to the efficient and powerful Least Cost Router (LCR), the VoiceBlue GSM gateway always chooses the cheapest possible way to route the call (according to GSM prefixes, free minutes on SIM cards etc.).Using the VoiceBlue gateway you achieve complete independence when connecting to GSM networks and maintain 100% control of your GSM call costs. With the help of voice prompts and efficient Dynamic Clip Routing, VoiceBlue routes incoming GSM calls to the right IP phones.


**What is Asterisk?**

Asterisk is a software PBX originally developed by Mark Spencer. This software PBX is designed as an open source, which means that it can be downloaded from the Internet, namely from: http://www.asterisk.org/index.php?menu=download, installed and used without any limitations. The fact that the Asterisk is a software PBX indicates that VoIP is the native environment for this PBX. Asterisk supports VoIP using three protocols, which means that its users can use a wide range of VoIP telephones, both software ones and hardphones. 

Together with the above mentioned VoIP services, Asterisk supports connection of TDM equipment by means of PCI cards supporting both digital (ISDN PRI/BRI) and analog trunks. Since the PCI bus on the PC is not unlimited, Asterisk users employ as much VoIP equipment as possible in order to save the TDM interface for other services, such as PSTN connection, or use of analog telephones. This is why most Asterisk users use the Ateus VoiceBlue VoIP GSM gateway for their GSM calls. You find below how to interconnect these two types of equipment.
For more information on the Asterisk software solution see www.asterisk.org.

**Main scenario**

Suppose we have an IP network to which an Asterisk IP PBX, several SIP telephones and an 2N VoiceBlue VoIP-GSM gateway are connected. This typical configuration is shown in the figure below. Furthermore, suppose that the network is addressed as shown in the figure and GSM numbers are all numbers starting with 6,7,8 and containing 9 digits. For configuration simplicity, use SIM cards from one GSM provider.
Now say that all incoming calls are answered by the gateway, which replays the invitation message and waits for 10s for another DTMF dialling. After this timeout, the gateway dials extension 111, which is a dial-in to the operator.


**2N VoiceBlue Gateway Configuration**

Step-by-step configuration...ready in 5 minutes:-)

*System Parameters* 
Set information on the IP interface only ...address, mask, default gateway

*Ethernet Parameters*
SIP proxy (GSM->IP) 
instructs the gateway where to send the invite packet during a GSM-VoIP incoming call ...the Asterisk address in our case
SIP proxy (IP->GSM)
instructs the gateway from which IP address the gateway may receive SIP packets ...the Asterisk address in our case
SIP registrar
To enable incoming calls to Asterisk for some units, register the selected units as Friend types in the Asterisk system. Set the following parameters in VoiceBlue for registration:
SIP registrar ...equipment for which the gateway is to be registered (Asterisk)
Username ...username under which the gateway shall be registered
Password ...registration password

*Assignment to Groups*

Since SIM cards from one GSM provider are used, all you have to do is make sure that all GSM modules have been assigned to the first group.

*Network List*
Create a network list containing the prefix of your GSM destinations ...remember to keep the default number of digits (9 in our case).

*LCR Table*
Finally, sum up all settings in a single LCR table defining that all called numbers that match the network list 1 shall be routed via GSM group 1.

**Incoming Calls**


As far as incoming calls are concerned, the 2N VoiceBlue gateway has a relatively wide choice of possibilities. Besides call ignoring and rejection, you can mainly receive incoming calls as follows:

Receive a call and replay the DISA message
A simply modifiable message is stored in the gateway and replayed whenever an incoming call is answered. During this message, the calling subscriber can dial in to the required extension. 
Receive a call with the second dialtone
The same as with DISA; the only difference is that the user hears the second dialtone instead of the message.
Receive a call and forward it to the operator immediately
An incoming call can be forwarded to the operator either immediately or after a timeout.
The following configuration can be used as an example:

Asterisk IP PBX Configuration
Now add a few lines in the IP PBX configuration for both proper routing of outgoing calls to the VoiceBlue GSM gateway and receiving calls coming from the GSM gateway to Asterisk.

**Outgoing Calls**

The core of Asterisk connection lies in the /etc/asterisk/extensions.conf file.

Open this file in your favourite editor and add the following lines:
    
    exten => _6XXXXXXXX,1,Dial(SIP/${EXTEN:0}@10.0.0.20,,r)
    exten => _7XXXXXXXX,1,Dial(SIP/${EXTEN:0}@10.0.0.20,,r)
    exten => _8XXXXXXXX,1,Dial(SIP/${EXTEN:0}@10.0.0.20,,r)


Once you have saved and closed the file, restart Asterisk and from now on all calls starting with 6,7,8 should be routed to the VoiceBlue GSM gateway. 

Incoming Calls
It is recommended to make a little restriction for incoming calls to prevent unauthorised persons from calling over your system. 
Since Ateus VoiceBlue works with the SIP, modify the /etc/asterisk/sip.conf file where the VoiceBlue section could look as follows, for example:

    
    [voiceblue]
    type=friend
    disallow=all
    allow=alaw
    host=10.0.0.20
    username=voiceblue
    secret=password
    permit=10.0.0.20/255.255.255.255
    qualify=yes


Again, restart Asterisk after saving the file. After that, Asterisk will be ready to receive calls coming from the VoiceBlue GSM gateway.

**What to do in case of troubles?**

The first thing you should do when you find that there is something wrong is to run the trace in VoiceBlue ... this helps you locate the problem. First of all you can see immediately whether any SIP messages come to the GSM gateway and if so, check the called numbers for proper format. If this is OK too, review the complete communication listing both on the VoIP and GSM sides. 

To start tracing click on Tracing in the CTRL section. For recommended trace parameters see the figure below.

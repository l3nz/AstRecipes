---
layout: post
title:  IPMI sensors under Debian
date:   2008-09-05 13:51:55.0
tags:
 - temperature
 - sensors
 - ML310
 - lm_sensors
 - IPMI
 - Debian

categories: update
---

Monitoring hardware sensors can be extremely useful for a production server. Recent servers - in my case a quad-code HP ML310 G5 - often adhere to a standard called IPMI for hardware monitoring.

My Debian kernel does not automatically load the required IPMI drivers, though the kernel modules are actually present with the distribuited kernel (in my case a *2.6.18-6-686-bigmem #1 SMP*).

In order to make them work, I have to manually load IPMI modules:

    
    modprobe ipmi_si
    modprobe ipmi_devintf


They will log on /var/log/messages the fact that they are being loaded. At the end you should get something like:

    
    ml310:~# lsmod | grep ipmi
    ipmi_devintf            9320  0
    ipmi_si                34572  0
    ipmi_msghandler        32288  2 ipmi_devintf,ipmi_si


Userland access tools are available in a standard Debian package called *ipmitools*

    
    apt-get install ipmitools


Usage is quite easy... see below.

**Seeing which sensors are available**

    
    ml310:~# ipmitool -I open sdr list
    Int. Health LED  | 0 unspecified     | ok
    VRM (CPU1)       | 0 unspecified     | cr
    CPU Fan          | 60.37 unspecifi | nc
    Rear Fan         | 33.32 unspecifi | nc
    Temp 1           | 24 degrees C      | ok
    Temp 2           | 30 degrees C      | ok
    Temp 3           | 19 degrees C      | ok


**Getting sensor details**

    
    ml310:~# ipmitool -I open sdr get 'Temp 1'
    Sensor ID              : Temp 1 (0x5)
     Entity ID             : 7.1 (System Board)
     Sensor Type (Analog)  : Temperature
     Sensor Reading        : 24 (+/- 0) degrees C
     Status                : ok
     Positive Hysteresis   : Unspecified
     Negative Hysteresis   : Unspecified
     Minimum sensor range  : -127.000
     Maximum sensor range  : Unspecified
     Event Message Control : Entire Sensor Only
     Readable Thresholds   :
     Settable Thresholds   : ucr unr
     Threshold Read Mask   : ucr unr


**See also**


* [IPMI tool man page](http://ipmitool.sourceforge.net/manpage.html)







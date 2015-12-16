---
layout: post
title:  Removing Asterisk
date:   2006-11-09 17:45:04.0
tags:
 - remove
 - delete
 - Asterisk

categories: update
---

It is sometimes necessary to completely remove Asterisk for one machine, for example because you need to install a newer version. 

**Stop Asterisk and unload its modules**

The first thing you have to do is to stop Asterisk and unload the modules it may be using, e.g Zaptel's.

The following lines will brutally terminate Asterisk and kill all ongoing conversation. You have to kill safe_asterisk first, otherwise it will respawn Asterisk.

    
    killall -9 safe_asterisk
    killall -9 asterisk


Then you'll have to unload the Zaptel drivers; check which ones are loaded by issuing a:

    
    [root@zebru]# lsmod | grep zaptel
    zaptel                214820  2 wcfxo,wctdm
    crc_ccitt               2113  1 zaptel


This means that the submodules *wcfxo* and *wctdm* are loaded for zaptel. We'll have to remove them in reverse order:

    
    modprobe -r wcfxo
    modprobe -r wctdm
    ..repeat for all zaptel submodules....
    modprobe -r zaptel


If you repeat the *lsmod | grep zaptel* command now, it should find nothing.

**Delete Asterisk files**

By running the commands below, you will delete with no possible recovery an Asterisk system. First make a backup of things you'd like to keep, lik ethe log files or the configuration files. 

* *Remember: once you run these commands, there's no turning back!*

    
    rm -rf /etc/asterisk
    rm -f  /etc/zaptel.conf
    rm -rf /var/log/asterisk
    rm -rf /var/lib/asterisk
    rm -rf /var/spool/asterisk
    rm -rf /usr/lib/asterisk


Now your Asterisk system has been completely removed.

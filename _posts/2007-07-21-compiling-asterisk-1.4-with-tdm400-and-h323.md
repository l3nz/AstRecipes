---
layout: post
title:  Compiling Asterisk 1.4 with TDM400 and H323
date:   2007-07-21 14:03:47.0
tags:
 - zaptel
 - Tdm400
 - h323
 - compiling
 - Asterisk
 - 1.4
 - #popular
 - #frontpage

categories: update
---

As a point of reference, we imagine you're going to build your new Asterisk on a machine preinstalled with TrixBox; this means your machine is very easy to reinstall from scratch if anything goes wrong. This also means that you can start playing with Asterisk 1.4 in a virtual machine, so you have no risk. 

The first thing you have to do is to create a working directory for Asterisk (say /src/asterisk14) and use it as a base directory
It will also be necessary to erase the existing Asterisk system - see [Removing Asterisk]({% post_url 2006-11-09-removing-asterisk %}). Make sure neither Asterisk nor its required libs and drivers (eg Zaptel) are running before attempting the installations.

You'll also need a bit more software:
* The C++ developement environment
* The Iksemel library, in order to test the Google Talk integration (we sure want that!)
* The Gnu TLS package, as needed by Iksemel
* Kernel development headers

**Prerequisites**

Getting all this software is fairly easy to get installed, thanks to the very powerful yum installed that ships with CentOS:

    
    yum install gcc-g++
    yum install gnutls-devel
    yum install kernel-devel


The first command may download and update quite a lot of stuff; don't worry and let it run.

**Installing Iksemel**

You will then download the Iksemel v1.2 libary ([http://code.google.com/p/iksemel/](http://code.google.com/p/iksemel/)) and compile it like this:

    
    wget http://iksemel.googlecode.com/files/iksemel-1.2.tar.gz
    tar zxvf iksemel-1.2.tar.gz
    cd iksemel-1.2
    ./configure
    make
    make install
    cd ..



**Checking you have the correct version of Make**

To make sure that you version of GNU Make is > 3.79, just run 

    
    [root@aleph asterisk]# make -v
    GNU Make 3.81


If the version of Make is < 3.80, you should update it or Asterisk won't compile - see [http://forums.digium.com/viewtopic.php?t=12707](http://forums.digium.com/viewtopic.php?t=12707). Most modern systems should not be affected by this, but this was the default version of make for RHEL 3 / CentOS 3; in this case, you should update it, either by using a RPM version or by compiling the new version of make right on your box. This should be very easy to do:

    
    cp /usr/bin/make /usr/bin/make_OLD
    wget http://ftp.gnu.org/pub/gnu/make/make-3.81.tar.gz
    tar zxvf make-3.81.tar.gz
    cd make-3.81
    ./configure
    make
    make install
    cd ..
    
    which make
    make -v


If now you're running Make 3.81, you're just fine.

**Download and install OpenH323 libraries**

We have to download and install the correct version of OpenH323. Even if there is version 1.19 available as well, it does not seem to compile correctly, so we will use version 1.18. We will install manually just the shared library we need, so compilation is very easy.

    
    wget http://downloads.sourceforge.net/openh323/pwlib-v1_10_3-src-tar.gz
    tar zxvf pwlib-v1_10_3-src-tar.gz
    cd pwlib_v1_10_3/
    ./configure
    make
    export PWLIBDIR=/src/asterisk-1.4/pwlib_v1_10_3
    cp $PWLIBDIR/lib/libpt_linux_x86_r.so.1.10.3 /usr/lib
    cd ..


We copy the shared library manually into the system folder.

It is very important that you set the PWLIBDIR to your path, othewise OpenH323 will not find it.

    
    wget http://downloads.sourceforge.net/openh323/openh323-v1_18_0-src-tar.gz
    tar zxvf openh323-v1_18_0-src-tar.gz
    cd openh323_v1_18_0/
    ./configure 


In the configure lines, check that it says something like: *PWLib prefix set to.... /src/ast14/pwlib_v1_10_3*

    
    make opt
    export OPENH323DIR=/src/asterisk-1.4/openh323_v1_18_0
    cp $OPENH323DIR/lib/libh323_linux_x86_r.so.1.18.0 /usr/lib
    cd ..


Again we must set the OPENH323DIR variable, so  Asterisk can know here to look for OpenH323. We copy the library manually to the system directory, as we do not care about the rest of OpenH323 features.

**Installing Asterisk 1.4.0**

First we have to download the latest version of Asterisk and Zaptel, as follows.

    
    wget ftp://ftp.digium.com/pub/zaptel/zaptel-1.4.4.tar.gz
    wget ftp://ftp.digium.com/pub/libpri/libpri-1.4.1.tar.gz
    wget ftp://ftp.digium.com/pub/asterisk/asterisk-1.4.8.tar.gz


We start by building Zaptel, as follows:

    
    tar zxvf zaptel-1.4.4.tar.gz
    cd zaptel-1.4.4
    ./configure
    make menuselect


At this point use the menuselect utility to select which Zaptel hardware - if any - you have on board. Please remeber that if you want to exit keeping the changes you made, you must press **x**
, while **q**
 will quit without saving and the **left arrow**
 will get you back to the previous menu. Even if you do not have Zaptel hardware, you should compile the *ztdummy* driver.

    
    make
    make install
    cd ..


If you get an error that says *Error: syntax error before "zone_lock"* then you have a small problem with you kernel sources; comment a single line in *spinlock.h* as suggested in [http://bugs.digium.com/view.php?id=6425](http://bugs.digium.com/view.php?id=6425) and it will be solved.

Now we build libpri - no configure or menuselect for it yet:

    
    tar zxvf libpri-1.4.1.tar.gz
    make
    make install
    cd ..


Now we build Asterisk itself:

    
    tar zxvf asterisk-1.4.8.tar.gz
    ./configure
    make menuselect


At this point, you should go to the Channels menu and see that H323 and chan_google are enabled. If it's not, vey likely you did not set OPENH323DIR or PWLIBDIR, so *configure* cannot enable H.323 by default.

    
    make
    make


When compiling with H.323 support, the first time you run make it will end with an error telling you to run *make* again. This is okay, just do it as it says. 

    
    make install
    make samples
    cd ..


Asterisk is now ready.

**Starting the TDM400**

We have a TDM 400 with 1 FXS channel on port 1 and 1 FXO channel on port 4, so we create the following files:

/etc/zaptel.conf
    
    loadzone = it
    defaultzone=it
    fxoks=1
    fxsks=4
    
    /etc/asterisk/zapata.conf
    [#CODE:]
    [channels]
    language=en
    ;
    ; FXS - telefono analogico
    ;
    signalling=fxo_ks
    context=interni_analogici
    group=1
    callwaiting=yes
    threewaycalling=yes
    transfer=yes
    cancallforward=yes
    channel => 1
    ;
    ; FXO - linea
    ;
    busydetect=yes
    busycount=5
    signalling=fxs_ks
    context=rete_pubblica
    group=2
    channel => 4


We start up the drivers by typing

    
    modprobe zaptel
    modprobe wctdm


And we expect no error messages save the correct output on /var/log/messages, something on the lines of:

    
    Oct 15 07:58:58 beth-pbx kernel: Zapata Telephony Interface Registered on major 196
    Oct 15 07:59:09 beth-pbx kernel: PCI: Found IRQ 9 for device 01:01.0
    Oct 15 07:59:09 beth-pbx kernel: Freshmaker version: 73
    Oct 15 07:59:09 beth-pbx kernel: Freshmaker passed register test
    Oct 15 07:59:09 beth-pbx kernel: Module 0: Installed -- AUTO FXS/DPO
    Oct 15 07:59:09 beth-pbx kernel: Module 1: Not installed
    Oct 15 07:59:09 beth-pbx kernel: Module 2: Not installed
    Oct 15 07:59:09 beth-pbx kernel: Module 3: Installed -- AUTO FXO (FCC mode)
    Oct 15 07:59:09 beth-pbx kernel: Found a Wildcard TDM: Wildcard TDM400P REV I (2 modules)
    Oct 15 07:59:09 beth-pbx kernel: Registered tone zone 0 (United States / North America)


**Starting it all together**

To run Asterisk now, type:

    
    ztcfg -v
    asterisk -vvvvvvvvvvvvvvvvvvvvvvvvvvc


Asterisk should load and start.

The ztcfg command is needed to apply the configuration information in /etc/zaptel.conf to the zaptel driver. Your fresh copy of Asterisk should be ready to run. If it crashes on startup complaining of missing dynamic library modules, just copy them to /usr/lib.

**Enabling H.323**

In order to enable H.323, you simply edit */etc/asterisk/h323.conf* as follows:

    
    gatekeeper = DISCOVER
    context=h323


This will tell it to find the gatekeeper automatically and to route incoming calls to context h323.
To place an outbound call fron Asterisk, you just dial:

    
    exten => 21,1,Dial(h323/21)


This will find the terminal 21 through the Gatekeeper and will call it as needed.


**See also:**

* [Installing the Asterisk GUI]({% post_url 2006-11-09-installing-the-asterisk-gui %}) for 1.4
* [Checking library dependencies]({% post_url 2005-05-02-checking-library-dependencies %})
* [Removing Asterisk]({% post_url 2006-11-09-removing-asterisk %})


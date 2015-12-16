---
layout: post
title:  Compiling Asterisk 1.2 with TDM400 and H323
date:   2005-10-17 14:35:01.0
tags:
 - zaptel
 - h323
 - 1.2
 - #frontpage

categories: update
---

This recipe shows how to:

* Compile Asterisk 1.2 (works fine with 1.2.4)

* Add the H.323 modules

* Start a TDM400 card with 1 FXS and 1 FXO port

This recipe was tested on CentOS 3.5 (kernel 2.4).

I suppose all sources will be downloaded and compiled in */src*.

If you are looking for information on installing Asterisk 1.4, see [Compiling Asterisk 1.4 with TDM400 and H323]({% post_url 2007-07-21-compiling-asterisk-1.4-with-tdm400-and-h323 %}).

**Downloading and compiling Asterisk**

First we build the zaptel drivers:
    
    cd /src
    wget http://ftp.digium.com/pub/zaptel/zaptel-1.2.3.tar.gz
    tar zxvf zaptel-1.2.3.tar.gz
    cd zaptel-1.2.3
    make
    make install
    cd ..


Now we build libpri:
    
    wget http://ftp.digium.com/pub/libpri/libpri-1.2.2.tar.gz
    tar zxvf libpri-1.2.2.tar.gz
    cd libpri-1.2.2
    make
    make install
    cd ..


Now we build Asterisk itself:
>>Please note that by 'make samples' you'll delete you existing configuration - Make a backup first!<<
    
    wget http://ftp.digium.com/pub/asterisk/asterisk-1.2.4.tar.gz
    tar zxvf asterisk-1.2.4.tar.gz
    cd asterisk-1.2.4
    make
    make install
    make samples


Now we build Asterisk addons: (optional)
    
    wget http://ftp.digium.com/pub/asterisk/asterisk-addons-1.2.1.tar.gz
    cd asterisk-addons-1.2.1
    make
    make install


Now we install Asterisk sounds too:
    
    wget http://ftp.digium.com/pub/asterisk/asterisk-sounds-1.2.1.tar.gz
    cd asterisk-sounds-1.2.1
    make install


**Compiling H.323 drivers**

The chan_h323 divers requires OpenH323 and a support library named PwLib. Make sure to download those exact versions! I use links to find the correct download URL.

    
    cd /src
    links http://prdownloads.sourceforge.net/openh323/
    
    wget http://umn.dl.sourceforge.net/sourceforge/openh323/pwlib-v1_9_0-src-tar.gz
    tar zxvf pwlib-v1_9_0-src-tar.gz
    cd pwlib_v1_9_0/
    ./configure
    make
    make install
    make opt
    
    PWLIBDIR=/src/pwlib_v1_9_0
    export PWLIBDIR


Now we fetch and compile OpenH323. Compilation will take quite a while, but that's okay.
    
    cd /src
    links http://prdownloads.sourceforge.net/openh323/
    
    wget http://umn.dl.sourceforge.net/sourceforge/openh323/openh323-v1_17_1-src-tar.gz
    tar zxvf openh323-v1_17_1-src-tar.gz
    cd openh323_v1_17_1/
    ./configure
    make
    make opt
    make install
    
    OPENH323DIR=/src/openh323_v1_17_1/
    export OPENH323DIR

It is very important to set PWLIBDIR and OPENH323DIR, because such variables will be used for further compiling.

Now we build the channel driver:
    
    cd /src/asterisk-1.2.4/chnnels/h323/
    make
    make opt
    cd ..
    cd ..
    make install


OpenH323 generates its libs in /usr/local/lib but my Linux uses /usr/lib for shared libraries, we'll need to copy the genberated libraries from /usr/local/lib to /usr/lib.
    
    cp /usr/local/lib/* /usr/lib


Now we make sure the channel driver exists and is loadable:
    
    ldd /usr/lib/asterisk/modules/chan_h323.so


There should be no missing references at this point.


**Startingthe TDM400**

I have a TDM 400 with 1 FXS channel on port 1 and 1 FXO channel on port 4.

I create the following files:
*/etc/zaptel.conf*
    
    loadzone = it
    defaultzone=it
    fxoks=1
    fxsks=4


*/etc/asterisk/zapata.conf*
    
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


I start the driver by typing
    
    modprobe zaptel
    modprobe wctdm

And I expect no error messages save the correct output on /var/log/messages, something like:
    
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

To run Asterisk type:
    
    ztcfg -v
    asterisk -vvvvvvvvvvvvvvvvvvvvvvvvvvc


Asterisk should load and start.
The *ztcfg* command is needed to applythe configuration information in */etc/zaptel.conf* to the zaptel driver.

When you are done with it, type "stop now".



---
layout: post
title:  Compiling Asterisk 12 on CentOS 6.5
date:   2013-12-30 13:45:44.0
tags:
 - PJSIP
 - CentOS
 - Asterisk
 - #popular
 - #frontpage

categories: update
---

Compiling Asterisk 12 (with PJSIP support) on a brand-new CentOS 6 system is pretty straightforward. Most of the packages come prebuilt so it's not very complex to do.

**Prerequisites**


Let's check the current version.

    
    [root@localhost ~]# cat /etc/redhat-release
    CentOS release 6.5 (Final)
    [root@localhost ~]# uname -a
    Linux localhost.localdomain 2.6.32-431.el6.i686 #1 SMP Fri Nov 22 00:26:36 UTC 2013 i686 i686 i386 GNU/Linux


First we update the system so that we have everything needed to compile plus the packages we need.

    
    yum update
    yum install gcc-c++ make gnutls-devel kernel-devel libxml2-devel ncurses-devel subversion doxygen texinfo curl-devel net-snmp-devel neon-devel
    yum install uuid-devel libuuid-devel sqlite-devel sqlite git speex-devel gsm-devel


**Compiling PJSIP**

We have everything for PJSIP but the SRTP library.

    
    wget http://srtp.sourceforge.net/srtp-1.4.2.tgz
    tar zxvf srtp-1.4.2.tgz
    cd srtp
    autoconf
    ./configure
    make
    make install
    cp /usr/local/lib/libsrtp.a /lib
    cd ..


Now we are ready for PJSIP itself. Make sure you download the patched version that works with Asterisk.

    
    git clone https://github.com/asterisk/pjproject pjproject
    cd pjproject/
    ./configure --prefix=/usr --enable-shared --disable-sound --disable-resample --disable-video --disable-opencore-amr --with-external-speex --with-external-srtp --with-external-gsm
    make dep
    make
    make install
    cd ..


*Note:* if compiling on a 64-bit CentOS system, remember to add *--libdir=/usr/lib64* to have Asterisk find it later (Thanks Jakub!)


**Compiling Asterisk**


We start by compiling Jansson - it is available in the CentOS repos, but it's an old version.

    
    wget http://www.digip.org/jansson/releases/jansson-2.5.tar.gz
    tar zxvf jansson-2.5.tar.gz
    cd jansson-2.5
    ./configure --prefix=/
    make
    make install
    cd ..


*Note:* I had to set *--prefix=/usr/* to stop Asterisk borking when detecting the Jansson library. (Centos 6.5 (Final) 64-bit)

Now it's time for Asterisk itself:

    
    wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-12-current.tar.gz
    cd asterisk-12.0.0/
    ./configure
    make menuselect


Under *Channel Drivers* check that *chan_pjsip* is checked (and disable *chan_sip* is you really feel brave!).
If building on a KVM box, better uncheck *BUILD_NATIVE* under *Compiler Flags* (press **x**
 to save).

    
    make 
    make install
    make samples
    cd ..


If all went well...

    
    [root@localhost asterisk-12.0.0]# asterisk
    [root@localhost asterisk-12.0.0]# asterisk -vvvvr
    Asterisk 12.0.0, Copyright (C) 1999 - 2013 Digium, Inc. and others.
    Created by Mark Spencer <markster@digium.com>
    Asterisk comes with ABSOLUTELY NO WARRANTY; type 'core show warranty' for details.
    This is free software, with components licensed under the GNU General Public
    License version 2 and other licenses; you are welcome to redistribute it under
    certain conditions. Type 'core show license' for details.
    =========================================================================
    Connected to Asterisk 12.0.0 currently running on localhost (pid = 18101)



**See also**

* [Getting started with ARI]({% post_url 2013-12-30-getting-started-with-ari %})







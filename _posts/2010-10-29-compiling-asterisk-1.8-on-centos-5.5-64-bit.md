---
layout: post
title:  Compiling Asterisk 1.8 on CentOS 5.5 64-bit
date:   2010-10-29 16:23:14.0
tags:
 - x86_64
 - snmp
 - ical
 - gtalk
 - dahdi
 - curl
 - compiling
 - centos
 - calendar
 - Asterisk
 - 64bit
 - 1.8
 
categories: update
---

After the first official release of Asterisk 1.8, I decided to test how it is and compiled it on a clean-slate 64-bit CentOS system. The experience went very smoothly and the *configure* script was able to pick up all the packages needed to activate various interesting extensions (it was not always so with previous releases).

Our target is compiling Asterisk 1.8.0 with the following features/extensions:
 - MySQL storage and dialplan
 - DAHDI drivers (I still call them Zaptel!) for analog lines
 - Full docs
 - CURL available for usage and dialplan
 - HTTP integration
 - SNMP agents
 - Calendars (they look yummy!)
 - Google Talk integration

**Setting up the environment**


The following packages have to be installed in order to allow compilation of all required modules. Some of these packages may or may not be already installed on your system.
I use as a reference a CentOS 5.5 x86_64 with kernel 2.6.18-194.17.4.el5-x86_64.

    
    [root@Centos64]# uname -a
    Linux Centos64 2.6.18-194.el5 #1 SMP Fri Apr 2 14:58:14 EDT 2010 x86_64 x86_64 x86_64 GNU/Linux


The following packages should all be present to allow compiling Asterisk:

    
    yum install gcc-c++ make gnutls-devel kernel-devel
    yum install libxml2-devel ncurses-devel subversion doxygen texinfo


And these are optional and required for specific modules to build:

    
    yum install curl-devel net-snmp-devel neon-devel


We will compile under */usr/src* .

**Compiling DAHDI and tools and libpri**


Since the days of Zaptel, we now have to install both DAHDI and its tools. 
Luckily both are packaged together and can be compiled in a single pass.

I immediately relize that the symbolic link used to find the kernel top *.configure* is broken; in my freshly-installed CentOS system */lib/modules/2.6.18-194.el5/build* is a link 
to a non-existent *../../../usr/src/kernels/2.6.18-194.el5-x86_64*. Finding the correct location for the kernel build is easy-peasy anyway; instead of correcting the link, I
prefer to override the check using an environment variable:

    
    export KSRC=/usr/src/kernels/2.6.18-194.17.4.el5-x86_64
    wget http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-2.4.0+2.4.0.tar.gz
    tar zxvf dahdi-linux-complete-2.4.0+2.4.0.tar.gz
    cd dahdi-linux-complete-2.4.0+2.4.0
    make
    make install
    cd ..


Internet connectivity is needed to download the firmwares for the various modules to be built - wouldn't  it be better to package them with the main files?

Compiling libpri is - as always - a breeze:

    
    tar zxvf libpri-1.4.12-beta2.tar.gz
    cd libpri-1.4.12-beta2
    make
    make install
    cd ..


**Compiling optional dependencies**


A couple of libraries are not availble in the CentOS repository and need to be installed manually to enjoy the benefits of 1.8.

The first is *iksemel*, an XML parser library for Jabber applications that is needed for GTalk integration:

    
    wget http://iksemel.googlecode.com/files/iksemel-1.4.tar.gz
    tar zxvf iksemel-1.4.tar.gz
    cd iksemel-1.4
    ./configure
    make
    make install
    cd ..


The other one is *libical*, an iCal library used to activate integration with iCal and WebDAV calendars:

    
    wget http://sourceforge.net/projects/freeassociation/files/libical/libical-0.44/libical-0.44.tar.gz/download
    tar zxvf libical-0.44.tar.gz
    cd libical-0.44
    ./configure --enable-shared
    make
    make install
    cd ..


I used version 0.44 because the 0.46 version does not compile out-of-the-box - it misses the template Makefiles from the downloadable archive. By the way, how many projects on the Internet bear the very  name name *libical*? please... On the other side, by adding this library, we get res_calendar_caldav,  res_calendar_exchange and res_calendar_icalendar - that's not
too bad.


If you also want to build the PDF manual (but you don't need to, you will need an optional tool called rubber that compiles Latex into a PDF:

    
    wget http://launchpad.net/rubber/trunk/1.1/+download/rubber-1.1.tar.gz
    tar zxvf rubber-1.1.tar.gz
    cd rubber-1.1
    ./configure
    make
    make install
    cd ..

  
Run target *make pdf* after building Asterisk.




**Compiling Asterisk 1.8**


Nothing could be easier than building Asterisk now.

    
    wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-1.8.0.tar.gz
    tar zxvf asterisk-1.8.0.tar.gz
    cd asterisk-1.8.0
    ./configure
    make menuselect
    make
    
    #this is ony for format MP3 - SVN required
    contrib/scripts/get_mp3_source.sh
      
    make install
    make samples
    make progdocs


After running menuselect, choose the modules you want to build. 

I always build docs so that I can copy them to an intranet webserver sand use them as a local reference in case it's needed. Asterisk 1.8 comes with an almost 300-page User Manual available under *doc/tex/asterisk.pdf*.

**Starting Asterisk**


Start Asterisk by typing:

    
    asterisk 


This will start Asterisk in the background. 

    
    [root@Centos64]# asterisk -rx "core show version"
    Asterisk 1.8.0 built by root @ Centos64 on a x86_64 running Linux on 2010-10-28 16:12:05 UTC


Will display the Asterisk version.

    
    asterisk -r


Will connect you to the Asterisk shell. Good luck!


**See also**

* [Compiling Asterisk 1.4 with TDM400 and H323]({% post_url 2007-07-21-compiling-asterisk-1.4-with-tdm400-and-h323 %})
* [Compiling Asterisk with OH323]({% post_url 2005-04-25-compiling-asterisk-with-oh323 %})
* [Installing the Asterisk GUI]({% post_url 2006-11-09-installing-the-asterisk-gui %}) for 1.4
* [Removing Asterisk]({% post_url 2006-11-09-removing-asterisk %})

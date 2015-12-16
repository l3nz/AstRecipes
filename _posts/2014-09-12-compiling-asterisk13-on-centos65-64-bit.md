---
layout: post
title:  Compiling Asterisk13 on Centos65 64-bit
date:   2014-09-12 11:08:43.0
tags:
 - PJSIP
 - CentOS
 - Asterisk
 - #popular
 - #frontpage

categories: update
---

This is a quick tutorial to get started with Asterisk 13 (currently beta) on Centos 6.5 64-bit.

Luckily the installation procedure is very similar to Asterisk 12 and it is very easy to go through.


First we disable selinux and update the system and install binary dependencies - it may take a while.

Disable selinux:
    
    sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
    reboot


Note that if you copy the commands below that you must either 1) make single commands all on one line, or 2) add back slashes to carry over to a new line (this wiki does not display the back slashes).

    
    yum update
    yum install -y screen lsof mlocate wget
    yum install -y gcc-c++ make gnutls-devel kernel-devel libxml2-devel ncurses-devel \
      subversion doxygen texinfo curl-devel net-snmp-devel neon-devel \
      uuid-devel libuuid-devel sqlite-devel sqlite \
      git speex-devel gsm-devel


Installing Jansson

    
    wget http://www.digip.org/jansson/releases/jansson-2.5.tar.gz
    tar zxvf jansson-2.5.tar.gz
    cd jansson-2.5
    ./configure --libdir=/usr/lib64
    make & make install
    cd ..


Installing SRTP

    
    wget http://srtp.sourceforge.net/srtp-1.4.2.tgz
    tar zxvf srtp-1.4.2.tgz
    cd srtp
    autoconf
    ./configure --enable-pic --libdir=/usr/lib64
    make && make install
    cd ..


Installing PJSIP

    
    git clone https://github.com/asterisk/pjproject pjproject
    cd pjproject
    ./configure --libdir=/usr/lib64 --prefix=/usr --enable-shared \
                 --disable-sound --disable-resample --disable-video --disable-opencore-amr \
                 --with-external-speex --with-external-srtp --with-external-gsm 
    make dep && make && make install
    cd ..


Downloading and installing Asterisk 13

    
    wget http://downloads.asterisk.org/pub/telephony/asterisk/releases/asterisk-13.0.0-beta1.tar.gz
    tar zxvf asterisk-13.0.0-beta1.tar.gz
    cd asterisk-13.0.0-beta1
    ./configure --libdir=/usr/lib64
    make menuselect


Note that as of 2014 November this works with Asterisk 13.0.0 too now that it is out of beta. Just substitute the current URL for the wget and other commands above, for example:
    
    wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-13-current.tar.gz


Under Channels you chack that there is  "
* chan_pjsip"

    
    make &&  make install &&  make samples
    cd ..


Running Asterisk

    
    asterisk 
    asterisk -vvvr


And you get

    
    localhost*CLI> core show version
    Asterisk 13.0.0-beta1 built by root @ localhost.localdomain on a x86_64 running Linux on 2014-09-12 08:28:08 UTC


Happy hacking!

..and of course, if you implement call-centers in Asterisk, do not forget to check out [QueueMetrics](http://queuemetrics.com) for reporting and [WombatDialer](http://wombatdialer.com) for implementing outbound added value services. :)
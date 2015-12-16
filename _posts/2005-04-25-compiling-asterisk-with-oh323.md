---
layout: post
title:  Compiling Asterisk with OH323
date:   2005-04-25 15:01:54.0
tags:
 - OH323
 - compiling
 - #popular

categories: update
---

**Getting Asterisk**


    
    cd /root/asterisk1/src
    export CVSROOT=:pserver:anoncvs@cvs.digium.com:/usr/cvsroot
    cvs login   ( the password is anoncvs.)
    cvs checkout zaptel libpri asterisk 


To get the current stable release, issue the following command: 
    
    cvs checkout -r v1-0 zaptel libpri asterisk asterisk-addons asterisk-sounds


**Getting OH323**

You'll need: 
Asterisk-OH323 
OpenH323 (v1.13.5) 
PWlib (v1.6.6) 
It is very important that you download the exact version of these packages, otherwise OH323 might not work. 

    
    wget http://www.inaccessnetworks.com/projects/asterisk-oh323/download/asterisk-oh323-0.6.5.tar.gz
    
    links http://prdownloads.sourceforge.net/openh323/openh323-Janus_patch4-src-tar.gz?download
    
    links http://prdownloads.sourceforge.net/openh323/pwlib-Janus_patch4-src-tar.gz?download


**Compiling Asterisk**


make
make install
make samples

**Patching oh323**

cd openh323
patch -p1 < asterisk-oh323-0.6.5/openh323_1.13.5-make.patch

**Compiling PWLib**


cd pwlib
./configure
make clean
make
make install
make opt

**Compiling OH323**


cd openh323
./configure
make clean
make opt


**Compiling ASTERISK-OH323**

You have to edit the makefile 
    vi Makefile


You should then modify the following lines to set your current path: 
    
    PWLIBDIR=/root/asterisk1/src/pwlib
    OPENH323DIR=/root/asterisk1/src/openh323
    ASTERISKINCDIR=/root/asterisk1/src/asterisk/include


To end compilation, you should: 

make
make install

**Checking the module**

To chehk that the module has been built, try 
    
    ls -l /usr/lib/asterisk/modules/chan_oh323.so

If you don't need debugging symbols, you can reduce the executable size with strip 
    
    strip /usr/lib/asterisk/modules/chan_oh323.so


**Start Asterisk**

Start Asterisk with: 
    
    asterisk -vvvvvvc


And see if it does start or it abends.

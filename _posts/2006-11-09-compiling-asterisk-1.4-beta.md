---
layout: post
title:  Compiling Asterisk 1.4 beta
date:   2006-11-09 17:04:05.0
tags:
 - compiling
 - beta
 - Asterisk
 - 1.4
 - #frontpage

categories: update
---



* You'll likely want to see [Compiling Asterisk 1.4 with TDM400 and H323]({% post_url 2007-07-21-compiling-asterisk-1.4-with-tdm400-and-h323 %}) for the full release of Asterisk 1.4 
*

If you want to check out what the buzz about the new Asterisk 1.4 is, there is one thing you got to do - download it and install it on your box. The compilation procedure is a bit different from the one of Asterisk 1.2, so we thought a brief tutorial would be helpful.

As a point of reference, we imagine you're going to build your new Asterisk on a machine preinstalled with TrixBox; this means your machine is very easy to reinstall from scratch if anything goes wrong. This also means that you can start playing with Asterisk 1.4 in a virtual machine, so you have no risk. 

The first thing you have to do is to create a working directory for Asterisk (say */src/asterisk14beta*)and download the latest version of Asterisk 1.4  beta in it; we have used the following:

* Asterisk Version 1.4.0-beta3

* Zaptel Version 1.4.0-beta2

* Libpri Version 1.4.0-beta1

It will also be necessary to erase your existing Asterisk system - see [Removing Asterisk]({% post_url 2006-11-09-removing-asterisk %}). Make sure neither Asterisk nor its required libs are running before attempting the installations.

You'll also need a bit more software:

* The C++ developement environment

* The Iksemel library, in order to test the Google Talk integration (we sure want that!)

* The Gnu TLS package, as needed by Iksemel

**Prerequisites**

Getting all this software is fairly easy to get installed, thanks to the very powerful **yum**
 installed that ships with CentOS:

    
    yum install gcc-g++
    yum install gnutls-devel


The first command may download and update quite a lot of stuff; don't worry and let it run.

You will then download the [Iksemel v1.2](http://iksemel.jabberstudio.org/) libary (first link on the left bar) and compile it like this:

    
    tar zxvf iksemel-1.2.tar.gz
    cd iksemel-1.2
    ./configure
    make
    make install


**Installing Asterisk**

The process of installing Asterisk is quite easy, as always. The most important changes have been the adoption of the standard GNU configure utility and a very nice menu configuration utility that will allow a simpler control on what you actually need to build.

    
    tar zxvf zaptel-1.4.0-beta2.tar.gz
    cd zaptel-1.4.0-beta2
    ./configure
    make menuselect


At this point use the menuselect utility to select which zaptel hardware - if any - you have on board. Please remeber that if you want to exit keeping the changes you made, you must press **x**
, while **q**
 will quit without saving.

The rest of the build process is as always was, so:

    
    make
    make install
    cd ..


The Libpri library at the moment does not have the fancy new build method, so we revert to a traditional:

    
    tar zxvf libpri-1.4.0-beta1.tar.gz
    cd libpri-1.4.0-beta1
    make
    make install
    cd ..


Now for Asterisk itself; the build process is simple as always. Make sure you have cleaned up your machine before proceeding, to avoid having modules compiled for 1.2 together with stuff for 1.4.

    
    tar zxvf asterisk-1.4.0-beta3.tar.gz
    cd asterisk-1.4.0-beta3
    ./configure
    make menuselect


Have a tour with the menu select utility. This is a great step forward! it will state clearly which modules you can build and which you cannot, and why. It's also very easy to turn off stuff you do not want, like channel drivers for protocols you do not need. See how there is a lot of new stuff in 1.4 - we'll have to spend some time studying it in order to catch up with new stuff! :)

    
    make
    make install
    make samples
    cd ..


That's it. Your Asterisk is now ready to rock! Start it up with 

    
    asterisk -vvvvvvvvvvvvvvc


And check the current version with the following command:

    
    show version


Here we are. Nice experimenting!

We can try a few new things, like:

* [Installing the Asterisk GUI]({% post_url 2006-11-09-installing-the-asterisk-gui %})





---
layout: post
title:  Compiling Asterisk on EPIA with VIA C3
date:   2008-03-28 22:24:32.0
tags:
 - VIA
 - frontpage
 - EPIA
 - C3
 - Asterisk
 - #frontpage
 - compiling

categories: update
---

Some EPIA boards are based on the VIA C3 processor, that is basically a clone of an i686 that does not support the whole instruction set. Therefore, in order to be sure that what you try to execute will actually work, you have to compile as if the processor were an i586, though Debian Etch thinks to be on an i686:

    
    gimel:~/asterisk-1.4.18.1# uname -a
    Linux gimel 2.6.18-5-486 #1 Fri Jun 1 00:07:22 UTC 2007 i686 GNU/Linux


As CentOS does not seem to be working on this kind of boards, we installed from scratch using a Debian compiled for i486, with the usual developement tools. As always, we need the kernerl headers in order to compile, so we run:

    
    apt-get install linux-headers-2.6.18-5-486


As the deb archives seemed not to be able to download this package, we had to run an update in order to successfully install

    
    apt-get update
    apt-get install linux-headers-2.6.18-5-486


we then download the latest Asterisk version:

    
    wget http://downloads.digium.com/pub/asterisk/releases/asterisk-1.4.18.1.tar.gz
    wget http://downloads.digium.com/pub/zaptel/releases/zaptel-1.4.9.2.tar.gz
    wget http://downloads.digium.com/pub/libpri/releases/libpri-1.4.3.tar.gz


We first start by compiling Zaptel. Note that we have to tell it explicitly to compile for i586, so we do:

    
    tar zxvf zaptel-1.4.9.2.tar.gz
    cd zaptel-1.4.9.2
    ./configure


Unfortunately, the configure command will not configure the architecture - at this point, we have to manually modify the Makefile in order to detect the ARCH used; look for the line where it defines ARCH, comment it and add a new one, as in:

    
    #ARCH:=$(shell uname -m | sed -e s/i.86/i386/)
    ARCH=i586


Now we export CFLAGS, so that GCC knows we want i586:

    
    export CFLAGS="-march=i586"



And we proceed as usual:

    
    make menuconfig
    make
    make install
    cd ..


We now have to compile libpri, and again we want i586 code - it seems like there is no switch for that, but in CFLAGS we trust:

    
    tar zxvf libpri-1.4.3.tar.gz
    cd libpri-1.4.3
    make
    make install
    cd ..


Now let's go for Asterisk - Asterisk does have a handy switch for the configure script, but unfortunately it will not compile and crash with linking problems.

Therefore we do:

    
    cd asterisk-1.4.18.1
    ./configure --build=pentium


and then we edit the Makefile, so that we add explicit linking to libpthread and libresolv:

    
    ASTLDFLAGS+=$(LDOPTS) -lpthread -lresolv


(One could have done that by export CFLAGS="-lpthread -lresolv" )

and then we go on as usual:

    
    make menuconfig
    make
    make install
    make samples


The make part will be pretty slow - VIA C3's are not exactly number-crunchers.

That's all there is - but finding it out took the best part of an afternoon.

For your information, here is the show translations command for that machine:

    
             Translation times between formats (in milliseconds) for one second of data
              Source Format (Rows) Destination Format (Columns)
    
              g723 gsm ulaw alaw g726aal2 adpcm slin lpc10 g729 speex ilbc g726 g722
         g723    -   -    -    -        -     -    -     -    -     -    -    -    -
          gsm    -   -    6    6       15     6    5    26    -     -  110   15    -
         ulaw    -  23    -    1       11     2    1    22    -     -  106   11    -
         alaw    -  23    1    -       11     2    1    22    -     -  106   11    -
     g726aal2    -  32   11   11        -    11   10    31    -     -  115    1    -
        adpcm    -  23    2    2       11     -    1    22    -     -  106   11    -
         slin    -  22    1    1       10     1    -    21    -     -  105   10    -
        lpc10    -  55   34   34       43    34   33     -    -     -  138   43    -
         g729    -   -    -    -        -     -    -     -    -     -    -    -    -
        speex    -   -    -    -        -     -    -     -    -     -    -    -    -
         ilbc    -  43   22   22       31    22   21    42    -     -    -   31    -
         g726    -  31   10   10        1    10    9    30    -     -  114    -    -
         g722    -   -    -    -        -     -    -     -    -     -    -    -    -


**See also:**


* [Debian on VIA C3](http://five.nocrew.org/via/debian.html)

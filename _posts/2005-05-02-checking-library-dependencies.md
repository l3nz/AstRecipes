---
layout: post
title:  Checking library dependencies
date:   2005-05-02 16:44:23.0
tags:
 - modules
 - compiling
 - #frontpage

categories: update
---

This not is not - strictly speaking - an Asterisk issue; but I present it here as it sometimes pops up with Asterisk installations, after compiling shared libraries (for example, OH323).

A typpical example is in fact an error like the one here below:

    
     [chan_oh323.so]May  2 15:41:29 WARNING[-1218537120]: loader.c:242 ast_load_resource: liboh323wrap.so: cannot open shared object file: No such file or directory
    May  2 15:41:29 WARNING[-1218537120]: loader.c:423 load_modules: Loading module chan_oh323.so failed!


Here Asterisk is complaining that even if the module chan_oh323 does exit and is being loaded, it cannot be completely loaded because of some missing shared libraries.

**What is a shared library?**

A shared library is a piece of compiled code that can be shared by more than one program on your system. This way, utility libraries need not be embedded in each and every executable that is on your disk. The cons are that if you copy a program and it does not find the exact libraries it needs in the designated system folders, it will simply refuse to run.

**How do I find which shared libraries are required by a module?**

The command **ldd**
 is your friend. It will report something like: 
NO-PLUGIN:(CODE marker="3,4" :]
[root@vm lib](-NOTFOUND-root@vm lib)# ldd /usr/lib/asterisk/modules/chan_oh323.so
        liboh323wrap.so => /lib/liboh323wrap.so (0x00bf2000)
        libh323_linux_x86_r.so.1.13.5 => not found
        libpt_linux_x86_r.so.1.6.6 => not found
        libstdc++.so.5 => /usr/lib/libstdc++.so.5 (0x008fc000)
        libpthread.so.0 => /lib/tls/libpthread.so.0 (0x009ff000)
        libdl.so.2 => /lib/libdl.so.2 (0x00add000)
        libc.so.6 => /lib/tls/libc.so.6 (0x00111000)
        libm.so.6 => /lib/tls/libm.so.6 (0x0025c000)
        libgcc_s.so.1 => /lib/libgcc_s.so.1 (0x0034b000)
        /lib/ld-linux.so.2 => /lib/ld-linux.so.2 (0x00b98000)
[:CODE)

As you can see, some modules are missing and cannot be loaded.

**How do I add the correct shared libraries?**

If you compiled the packes yourself, chances are that the shared libraries you are looking for are present on your system, but in the wrong locations. 

To understand if that is the case, simply run the **locate**
 command, like here below:
    
    [root@vm lib]# locate libh323_linux_x86_r.so.1.13.5
    /root/asterisk/openh323/lib/libh323_linux_x86_r.so.1.13.5


It is important that them files have the exact (long) names referenced by the module.

In this case you simply copy your library to **/lib**
 and run **ldd**
 again to see if it can be loaded.

Repeat as needed.



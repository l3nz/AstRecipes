---
layout: post
title:  Changing DTMF tone frequency in Asterisk
date:   2007-02-22 17:18:02.0
tags:
 - DTMF
 - Asterisk
 - #frontpage

categories: update
---

When Asterisk is handling a call and needs to listen to that call, e.g. to monitor it for DTMF transfer tones, Asterisk will detect and rebuild all DTMF tones on that call. 

If you use Asterisk as a bridge to connect appliaces that communicate by exchanging DTMF tones (e.g. remote alarms trasmitting their alarm-ID, or other remote industrial appliances that were designed before the IP age), Asterisk will consistently intercept, buffer and regenate all DTMF tones it detects. This may be a problem, as some of those appliances use distinct DTMF-silence cadences, and by regenerating those tones they are output with Asterisk's signal-silence cadences. 
In order to avoid this behaviour in Asterisk, what you can do is to modify the frequency of the DTMF tones Asterisk expects, so that the true DTMF tones are not recognized and therefore let alone by the DSP module. 

**A bit of theory**

Any DTMF digit is built out of two distinct signals, chosen so that their harmonics do not overlap and are therefore easy to detect and hard to misinterpret. The basic DTMF system uses a telephone-keyboard style matrix of four columns by four rows, in exactly the same configuration that can be found in telephones all around the world (ordinary consumer telephones do not have the fourth column, but you get the idea)

    
      +--------+---------+---------+---------+---------+
      |        | 1209 Hz | 1336 Hz | 1477 Hz | 1633 Hz |
      +--------+---------+---------+---------+---------+
      | 697 Hz |   1     |   2     |    3    |    A    |
      | 770 Hz |   4     |   5     |    6    |    B    |
      | 852 Hz |   7     |   8     |    9    |    C    |
      | 941 Hz |   *     |   0     |    #    |    D    |
      +--------+---------+---------+---------+---------+


Therefore, to send a digit "3" the telephone will generate both a 697Hz and a 1477Hz tone at the same time.

**Changing the DTMF tones**

The Asterisk module **dsp.c**
 contains the definitions for DTMF tones. Just look for a part of the source code that says:

    
    static float dtmf_row[] =
    {
    	697.0,  770.0,  852.0,  941.0
    };
    static float dtmf_col[] =
    {
    	1209.0, 1336.0, 1477.0, 1633.0
    };


And change that to:

    
    static float dtmf_row[] =
    {
            732.0,  809.0,  894.0, 988.0
            /* 697.0,  770.0,  852.0,  941.0 */
    };
    static float dtmf_col[] =
    {
            1270.0, 1404.0, 1551.0, 1715.0
            /* 1209.0, 1336.0, 1477.0, 1633.0 */
    };


This will raise all detected DTMF codes by some 40Hz, enough for Asterisk to become completely tone-blind to existing DTMF codes.

Once you made the change, you must recompile Asterisk by:
1. Stopping Asterisk
2. Go to your Asterisk source directory
3. Enter *make clean*
4. Enter *make*
5. Enter *make install*

Note that you still can send Asterisk DTMF codes to transfer the call, start monitoring or what else - as long as you use the new DTMF tone matrix you just input. This is pretty easy to do with most industrial-grade call handling equipment.


**See also**


* [DTMF tones on Wikipedia](http://en.wikipedia.org/wiki/DTMF)



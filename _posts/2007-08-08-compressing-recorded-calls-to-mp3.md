---
layout: post
title:  Compressing recorded calls to MP3
date:   2007-08-08 21:44:50.0
tags:
 - WAV
 - sound
 - MP3
 - makefile
 - GSM
 - #frontpage

categories: update
---

If you record a big chunk of traffic (or even all traffic, as some call-centers do) on your Asterisk box, you will see that it ends up taking a substantial amount of disk space. The problem is the following:

* Recording wav files is very good in terms of CPU usage, but those files end up being huge

* Transcoding to GSM files still takes up substantial disk space and is much heavier on the CPU
The ideal would be saving those files in some very-compressed MP3  format, but this is not a very good idea as the hit on the CPU is quite dramatic.
Still, most systems do not operate on a 24/7 basis; if your call center gets a lot of traffic during office hours, it's just as well likely that it will be sitting idle all night.

Therefore, it would be nice if we could:

* Record all calls using the cheapest recording format

* Transform all calls to some very compressed format at night, when the CPU is sitting idle

This way, if you listen to a call that's just been recorded, you will download a large wav file; if you listen to a call that has been recorded two days ago, you will download a highly-compressed  MP3 file.

For example, take the standard *fpm-calm-river* file that is distribuited with Asterisk; its size is:

    
    +--------------+----------------+
    | wav file     |  1310764 bytes |
    | gsm file     |   135168 bytes |
    | mp3 file 16k |   164520 bytes |
    | mp3 file 12k |   120528 bytes |
    | mp3 file 8k  |    83808 bytes |
    +--------------+----------------+


The mp3 file is compressed as 16kbps/mono and sounds pretty good, likely better that the gsm file. The mp3 compressed at 8kbps/mono  sounds a bit compressed, but it stays intellegible.

**Enconding wav files into MP3**

To encode wav files into mp3, we create a make file. Make is the right tool for the job, as it is built to transform one file into a different file, by name. 

So we edit a file called Makefile (yes, with the capital M) into our main storage directory, usually */var/spool/asterisk/monitor*:

    
    DIRWAV = $(shell dir *.wav)
    ALLWAV = $(DIRWAV:.wav=.mp3)
    
    DIRGSM = $(shell dir *.gsm)
    ALLGSM = $(DIRGSM:.gsm=.mp3)
    
    mp3: $(ALLWAV) $(ALLGSM)
    
    
    %.mp3: %.wav
            nice lame --quiet --preset phone $? $@
            rm -f $?
    
    
    %.wav: %.gsm
            nice sox $? -r 8000 -c 1 -w -s $@
            rm -f $?
    
    
    clean:
            rm -f *.mp3
            rm -f *.wav
            rm -f *.raw
            rm -f *.gsm
            cp /var/lib/asterisk/mohmp3/*.wav .
            cp /var/lib/asterisk/sounds/a*.gsm .


(You can omit the clean section we used for testing). 

Leave a few blank lines after each target.

Make sure you have *sox* and *lame* installed on your machine.

Then at night you run a job like:

    
    cd /var/spool/asterisk/monitor; make mp3


At night, this makefile will search all wav (or gsm) files and convert them into mp3, deleting the original files. 

**Choosing 16k, 12k or 8k compression**

In order to fine-tune the mp3 encoding to your preferred size, you can change the parameter after --preset to:


* --preset phone  -> 16kbps/mono, high quality

* --preset 12     -> 12kbps/mono, normal quality

* --preset 8      -> 8 kbps/mono, low quality

If your system is not over-loaded, you may also want to pass the '-h' parameter to turn on high-quality (but slower) mp3 encoding.


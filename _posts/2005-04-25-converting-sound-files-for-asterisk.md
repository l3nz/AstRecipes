---
layout: post
title:  Converting sound files for Asterisk
date:   2005-04-25 15:08:14.0
tags:
 - WAV
 - sound
 - GSM

categories: update
---

I usually use Audacity to record my sound files as 16 bit 44khz WAV files.

To upload them in Asterisk, I use this small script that will create them in your destination directory as GSM files.

    
    #! /bin/bash
    
    FROM="/home/joe/samples"
    TO="/var/lib/asterisk/sounds"
    
    for i in $FROM/*.wav
    do
    echo $i
    sox $i -r 8000 -c 1 $TO/$(basename $i .wav).wav resample -ql
    done




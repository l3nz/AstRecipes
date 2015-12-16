---
layout: post
title:  Script to convert music-on-hold to native formats
date:   2006-03-14 04:17:12.0
tags:
 - #frontpage
 - moh
 - musiconhold
 - GSM
 - MP3

categories: update
---

This script converts a directory full of MP3s to another directory with the same files in native formats (WAV, GSM and U-Law) - see recipe [Music-on-hold without MPG123]({% post_url 2006-03-13-music-on-hold-without-mpg123 %}) for an overview of how this works.

    
    FROMDIR="/root/mp3s"
    TODIR="/root/transcodes"
    
    for i in $FROMDIR/*.mp3
    do
    BASEFILE=$(basename $i .mp3)
    echo Converting $BASEFILE
    
    mpg123 -s --rate 44100 --mono $FROMDIR/$BASEFILE.mp3 > $TODIR/$BASEFILE.raw
    sox -r 44100 -w -s -c 1 $TODIR/$BASEFILE.raw -r 8000 -c 1 $TODIR/$BASEFILE.wav
    sox $TODIR/$BASEFILE.wav -t gsm -r 8000 -b -c 1 $TODIR/$BASEFILE.gsm
    sox $TODIR/$BASEFILE.wav -t ul -r 8000 -b -c 1 $TODIR/$BASEFILE.pcm
    rm -f $TODIR/$BASEFILE.raw
    
    done


Please set FROMDIR and TODIR as you best see fit (they can also be the same directory).

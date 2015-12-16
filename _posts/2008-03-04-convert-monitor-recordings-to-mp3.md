---
layout: post
title:  Convert Monitor Recordings to mp3
date:   2008-03-04 22:51:33.0
tags:
 - WAV
 - script
 - MP3
 - monitor
 - crontab
 - cron
 - convert
 - Asterisk
 - 1.4
 - sound

categories: update
---

We run a small office with our Asterisk PBX but we record all phone traffic, in and out.  Needless to say, this can exact a large toll on our disk resources.  I'm submitting this script and related Cron entry for those who need to compress their monitor recordings to mp3 format and need a program that will only run when the PBX is idle.


Assumptions:


* Your PBX is Asterisk 1.4 or higher

* Your recordings are already being stored in wav format

* You have LAME installed and working properly

* You create the script as root, run it from /root directory
    and from root's Crontab


    
    #!/bin/sh
    # Program:  mon2mp3.sh 3-4-08 by Dan S
    # A little code to convert monitor recordings to mp3.
    # Only runs when there are no active calls!
    #
    cd /root
    export TERM=xterm
    SBIN="/var/spool/asterisk/monitor/"
    /usr/sbin/asterisk -rx "core show channels" >chan.txt
    AC=$( tail -1 chan.txt | awk '{ print $1 }' )
    if [ $AC == "0" ] ; then
            ls $SBIN*.wav >ls.txt
            while read LINE ; do
            WN="$LINE"
            MN="${LINE//wav/mp3}"
            lame --quiet -h $WN $MN
            chown asterisk:asterisk $MN
            chmod 644 $MN
            rm -f $WN
            done <ls.txt
    else
            exit
    fi
    exit


(Here's a quick breakdown of how it operates). 

The "TERM" variable is set to satisfy LAME's requirement.

We run the Asterisk show channels command and strip out the number of active calls.

If there are no active calls, we run the conversion section, otherwise exit.

We start the conversion section by saving a listing of wav files in the monitor directory.  If the list is empty (we are already caught up) the program will exit the loop and end.

If there are wav files in the directory, the loop will convert them to mp3, will fix the permissions on them, then delete the original wav files from the monitor directory.

By running the process only when there are no active calls, we ensure that all the wav files found are complete (not still being recorded or awaiting soxmix) and since the listing is a snapshot of the directory while Asterisk was idle, if active calls are started while this script is running, new wav files will not be processed until the next time the script is run.


I like the script to run often to minimize the backlog so
I have installed the script in Root's Crontab like this:

    
    0,5,10,15,20,25,30,35,40,45,50,55 * * * * sh mon2mp3.sh 2&>1


The "2&>1" reroute is there to keep the "ls" function in the script from spitting out an email to you whenever there are no wav files in the directory at the time the script is run, meaning the monitor directory is already up-to-date.


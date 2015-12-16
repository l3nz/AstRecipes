---
layout: post
title:  Music-on-hold without MPG123
date:   2006-03-13 10:40:59.0
tags:
 - sound
 - musiconhold
 - mpg123
 - moh
 - #frontpage

categories: update
---

When playing the music on hold, Asterisk will usually decode MP3 files using the external utility MPG123. This setup is less than perfect, as:

* MPG123 is run as an external process, so it must be launched once for every music class

* MPG123 will sometimes complain of the MP3 data used

* MPG123 will sometimes not loop the sound over, so the caller ends up listening to silence

* MPG123 will sometimes start eating up a lot of CPU with no apparent reason

* Live MP3 decoding is, in any case, an expensive, CPU-hungry activity

In this recipe, we'll show how to convert the MP3 files to a native WAV format and then use that as a source of sound. The resulting file is surely larger, buy the few megabytes of disk lost are usually well paid-for in Asterisk well-being.

First of all, create a directory for your new music on hold; we may create it as */var/lib/asterisk/moh-default*. All files in this directory will be played to the caller, one after the other. Of course, you can also have only one file with your music on hold if you have to.

The following commands will translate the standard file *fpm-sunshine.mp3* to a correctly formatted WAV file:

    
    mpg123 -s --rate 44100 --mono /src/mp3/fpm-sunshine.mp3 > fpm-sunshine.raw
    sox -r 44100 -w -s -c 1 fpm-sunshine.raw -r 8000 -c 1 fpm-sunshine.wav
    rm fpm-sunshine.raw


Repeat as you prefer for all the MP3 files you want to include. 

You then have to edit the *musiconhold.conf* file in order to tell Asterisk to play those files instead of the standard MP3's:

    
    [default]
    mode=files
    directory=/var/lib/asterisk/moh-default
    random=yes


Create an entry for each of the music classes you want to play (the one above will substitute the default one).

Restart Asterisk and make sure no more MPG123's are running.

From the Asterisk shell, use the **moh classes show**
 command to see which music classes are currently defined and **moh files show**
 to see the actual files being played by Asterisk.

If you have a number of files that you want to convert all in one pass, this [script to convert music-on-hold to native formats]({% post_url 2006-03-14-script-to-convert-music-on-hold-to-native-formats %}) may prove handy.

**Further avoiding transcoding**

When Asterisk plays back native MOH (as we are doing with this recipe), it checks the sound file by extension and tries to find the one with the minimum cost compared to the current in-use codec, just like it usually does for sound files. 
This means that if you have fpm-sunshine.wav and fpm-sunshine.gsm in your *moh-default* directory, and your telephone is using the GSM codec, Asterisk will try to use the .gsm file in order to avoid any transcoding at all. 
While transcoding from SLIN format (the one we used in the WAV file above) is usually the cheapest, if we know that our system uses GSM or U-Law very often, it could be a good idea to transcode to GSM and U-Law too; this is easily obtained with a couple of Sox calls:

    
    sox fpm-sunshine.wav -t gsm -r 8000 -b -c 1 fpm-sunshine.gsm
    sox fpm-sunshine.wav -t ul -r 8000 -b -c 1 fpm-sunshine.pcm


The first line transcodes from SLIN to GSM, the second from SLIN to ULaw. This helps keeping the system load to a minimum (playing music-on-hold accounts for a non-trivial portion of most PBXs live activity!)

**See also**


* [http://lists.digium.com/pipermail/asterisk-users/2006-February/141180.html](http://lists.digium.com/pipermail/asterisk-users/2006-February/141180.html) for the scalability implications of running native MOH as described in this recipe (they are good!)

* [http://lists.digium.com/pipermail/asterisk-users/2006-March/142108.html](http://lists.digium.com/pipermail/asterisk-users/2006-March/142108.html) a guide to recoding the audio data as needed by Asterisk codecs 

* A [script to convert music-on-hold to native formats]({% post_url 2006-03-14-script-to-convert-music-on-hold-to-native-formats %}) based on the ideas in this recipe.

Thanks to Matt R for input!

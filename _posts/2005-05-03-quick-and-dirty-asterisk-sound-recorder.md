---
layout: post
title:  Quick and Dirty Asterisk sound recorder
date:   2005-05-03 11:20:12.0
tags:
 - sound
 - recorder
 - GSM
 - extensions
 - #frontpage

categories: update
---

This script is a quick-and-dirty Asterisk sound recorder to be used to record sound files.

It records a sound file, plays it back and stores it as */var/lib/asterisk/sounds/mymessage.gsm*.

    
    [ast-recorder]
    ; quick-and-dirtyu sound recorder
    exten => s,1,Answer
    exten => s,2,Playback(registratore-invito)
    exten => s,3,Playback(beep)
    exten => s,4,Record(mymessage:gsm)
    exten => s,5,Playback(beep)
    exten => s,6,Playback(mymessage)
    exten => s,7,Background(registratore-post)
    exten => s,8,Wait(10)
    exten => s,9,Hangup
    exten => 2,1,Goto(s,4)



You have to provide a couple of voice messages:

* resgistratore-invito says something like *Sound recorder. Please record your message after the beep. Press pound to stop recording.*

* registratore-post says something like *Your message was stored. Press 2 to record it again.*

Or you could use Festival not to record the emssages yourself.





---
layout: post
title:  Play endless random sounds for testing
date:   2014-12-20 20:05:32.0
tags:
 - testing

categories: update
---

This Extension will play random sounds from asterisk standard sound set forever.
You might have to adjust the path uses. This example works on Debian:

	exten => endless,1,Answer()
	exten => endless,2,Set(RANDSOUND=${SHELL(ls /var/lib/asterisk/sounds/en | shuf -n 1 | head -n1 | cut -f1 -d"." | tr -d "\n")})
	exten => endless,3,NoOp(*${RANDSOUND}*)
	exten => endless,4,Playback(${RANDSOUND})
	exten => endless,5,Goto(2)
	exten => endless,n,Hangup()

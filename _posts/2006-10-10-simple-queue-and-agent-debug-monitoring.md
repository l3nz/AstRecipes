---
layout: post
title:  Simple queue and agent debug monitoring
date:   2006-10-10 13:01:11.0
tags:
 - queue
 - debug
 - cli
 - agents
 
categories: update
---

It may have happened to you: you get called by a busy call center to debug the functionalities of Asterisk, and you get lost in the continuous logging on the console done by tens of operators who keep on using the system. Asterisk produces a lot of information, but that very lot seems to be a bit discouraging for understanding what is going on. What we do now is automate some very simple filtering tasks so that we are not swamped anymore by unwanted information.

**Grepping the full log**

With a standard configuration, * will log everything to the */var/log/asterisk/full* log, that will easily get to be a hundred-megabyte behemot.

The first thing we do then is to rotate it, i.e.:

    
    mv /var/log/asterisk/full /var/log/asterisk/full.old
    asterisk -rx "logger rotate"


This will have Asterisk reopen the file, so that we get one anew. It may be wise to automate this task on a daily basis for busy CCs.

As we only want events that have to do with queues and agents, we filter it like this:

    
    tail -f full | grep -e'Agent' -e'queue' -i


This will produce a realtime output with only lines with the words "agent" or "queue" in it, i.e. the lines we care about. The logging is very detailed, so you can actually see the ACD deciding which agent to call, trying to call him and the resulting return codes.

**Seeing queues in real-time**

What we want to do now is to have a real-time panel, a bit like the command **top**
 in Linux, where we can see the queues we care about in real-time and with the minimum possible clutter.

We create a small script like the following one:

    
    until [ 20 -lt  10 ]; do
            clear
            date
    
            asterisk -rx "show queue q-one" | grep -v -i -e'Unavailable'
    
            asterisk -rx "show queue q-two" | grep -v -i -e'Unavailable'
            sleep 5
    
    done



It will print out the status of each queue we care about, kill the lines about unavailable agents to lessen the clutter, and go on like this in an infinite loop. Of course you can change it to show any queue you like, not just *q-one* and *q-two* as in the example above.

Please note that sometimes * will not return the correct data from the command execution, so the result may be a bit incostant.

**Seeing available agents and their statuses**

It is possible to show agents that are actually available on the system with a command very similar to the one above:

    
    asterisk -rx "show agents" | grep -v -e'not logged'


This does show each and every available or busy agents and in the case of busy the channel they're speaking to.

Happy debugging!


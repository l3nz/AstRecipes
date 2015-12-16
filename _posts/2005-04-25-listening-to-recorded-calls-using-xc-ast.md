---
layout: post
title:  Listening to recorded calls using XC-AST
date:   2005-04-25 15:07:19.0
tags:
 - xc-ast
 - monitor

categories: update
---

From version 0.8 onwards, XC-AST offers a way to listen to recorded calls made by your agents. Here are the steps you should follow:

Make sure it is legal
This is not strictly an Xc-Ast issue, but before attempting to record all calls on a queue, you should consult a lawyer to make sure it is legal in your country. It would be probably fair enought to add tell your operators their calls are being recorded and to add a voice message telling the customers their call will be recorded.

Tell Asterisk to record all calls
Telling Asterisk to automaticcally record all calls that will be processed by a queue is really simple; in extensions.conf you should add something like this:

exten => s,1,SetVar(MONITOR_FILENAME=/var/spool/asterisk/queuecalls/QSAMPLE-${UNIQUEID})
exten => s,2,Queue(q-sample|n|||)


I usually create a directory called queuecalls to hold all calls and the name each file with the name of the queue (QSAMPLE) followed by the call id. This is enough for all calls to be recorded. They will create files with names like this:



QSAMPLE-1092210263.122.gsm


Telling XC-AST where to look for the calls
You should set up the WEB-INF/configuration.property file in XC-AST like this:

default.monitored_calls=/var/spool/asterisk/queuecalls/

When looking for the recording of a call, XC-AST will explore all files contained in /var/spool/asterisk/queuecalls/ and any directories below for a file name containing the right call id. It might find more than one file name and will display all of them. It is possible that sometimes Asterisk fails at mixing together the two files (Asterisk records separate files for the caller and the agent, and then tries to mix them together at the end of the call) so you will find two files named -in and -out instead.

Telling XC-AST you have the right to listen to the calls
Any user willing to listen to calls must hold the key CALLMONITOR. This is to make sure that only authorized personnel can listen to recorded calls. If you do not have this key, XC-AST will not show any sound files.

Make sure XC-AST has the right to read the calls
You should make sure that the process running XC-AST (i.e. the servlet container, might be Tomcat, Jetty or something else) has the rights to access the files where recorded calls are stored. The webserver instead should not be able to access those files, as XC-AST will send files only after enforcing security checks.


This should be everything!
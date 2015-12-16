---
layout: post
title:  Listening to multi-folder recordings in queuemetrics
date:   2009-05-25 18:18:35.0
tags:
 - monitor
 - audio
 - #frontpage
 - QueueMetrics

categories: update
---

It is possible to have QueueMetrics search for recordings in multiple directories, e.g. one directory for each day. This has the great advantage of being faster (because QueueMetrics does not have to scan through the whole file set) and more manageable (because you can easily move files around by date e.g. from a local volume to a NAS unit as needed).

In order to do this, you have to configure "LocalFilesByDay" storage in QueueMetrics.

In this example, we imagine that your recordings are held in a directory structure like */recordings/YYYY/MM/DD/myfile.wav*

It is also mandatory that all files have in their name the unique-id of the call that was recorded, so that QM can match audio files to calls.


* **Edit configuration.properties**


You should set the following properties as in the following example:

    
    audio.server=it.loway.app.queuemetrics.callListen.listeners.LocalFilesByDay
    default.monitored_calls=/recordings/%YY/%MM/%DD/
    audio.lookBack=2


This means that:
* you set the correct audio server to be used (in our case LocalCallsByDay)
* you set the correct place where to look for the calls, based on the day they were made - note how there is a trailing slash, or the search won't work
* you tell QM that all calls made on a two-hour boundary to the beginning and the end of the day should be looked for in the previous or next day as well (so you can track e.g. transfers).


If you have a large number of calls already recorded, Loway offers a service for transfer to the correct directory structure.


* **See also:**

- [Pluggable Modules in QueueMetrics](http://queuemetrics.com/manuals/QM_UserManual-chunked/ar01s17.html) 
- [Storing audio files in subfolders]({% post_url 2009-05-25-storing-audio-files-in-subfolders %})



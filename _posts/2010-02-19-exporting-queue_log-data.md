---
layout: post
title:  Exporting queue_log data
date:   2010-02-19 10:59:03.0
tags:
 - queue_log
 - mysqldump
 - MySQL
 - export

categories: update
---

This query lets you export the contents of the queue_log table in SQL format for inspection. Instead of exporting the whole table, it exports only the period specified. 

    
    /usr/bin/mysqldump \\
      -h localhost \\
      -u queuemetrics -p \\
      --databases queuemetrics \\
      --tables queue_log \\
      --where="time_id between \\
               unix_timestamp('2008-10-23 00:00:00') \\
           and unix_timestamp('2008-10-28 00:00:00')" \\
      > outfile.sql


When you run an export, make sure that you express the time period in GMT time, as that is usually the one used by Asterisk.

If you have to send the file over for inspection, you can greatly reduce its size by compressing it, e.g.

    
    bzip2 outfile.sql


This will create a file called 'oufile.sql.bz2'.



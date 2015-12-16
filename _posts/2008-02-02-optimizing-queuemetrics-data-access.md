---
layout: post
title:  Optimizing QueueMetrics data access
date:   2008-02-02 16:03:56.0
tags:
 - QueueMetrics
 - performance
 - MySQL
 - database
 - #frontpage

categories: update
---

If you run a very large call center and your queue_log table starts to get big (>1M lines), you can apply the following techniques to improve database performance.

**1. Create a complete index**


The default index used for access, called "partizione_b", is not very efficient for huge datasets because its granularity is aimed only at partition and time period. To make an idex such that all queries can be "solved" completely through the index, you should drop other indices and create this one:

    
    ALTER TABLE `queue_log` ADD INDEX (   
    	partition(9), time_id, unique_row_count, queue(15) 
    )


Please note how we give a maximum size to each text field - make that as long as your largest entry on that field (see below).

**2. Use short partition and queue names**


The shorter names you use for your partitions and your queues, the more compact the index will be, so the faster will be the access. In no case make the queue index smaller than an used queue, or it wil have to go check on all rows if they match or not!

You can get information on how long are your fields by running the following queries:

    
    SELECT DISTINCT partition, length( partition ) AS L FROM queue_log


and

    
    SELECT DISTINCT queue, length( queue ) AS L FROM queue_log


Once you get this, I suggest using one or two characters for the partition (P1 or S3 or even A, B C...) and short names for the queues (withing 5-6 characters). Then you create the index as described in #1 using the new field lengths.

**3. Make a log of slow queries**

If your performace is suboptimal, you should make a log of slow queries. This is easily done in MySQL: see the article [MySQL's Over-looked and Under-worked Slow Query Log](http://www.databasejournal.com/features/mysql/article.php/2013631). I suggest setting a limit of 5 seconds to tell a long query from a normal one.

You should then look for entries like:

    
    Query_time: 6  Lock_time: 0  Rows_sent: 12999  Rows_examined: 988483


This means that the query took 6 seconds to run, 12999 rows were sent to the client but 988943 were examined.  Better indexing will make those divergences be smaller.

You can also run

    
    mysqldumpslow name-of-slow-query-log


To see a clearer report.

**4. Optimize the table and its indices**


The table should be optimized by partition and access time. You can do that by running:

    
    ALTER TABLE queue_log 
    ORDER BY partition, time_id, unique_row_count


This operation will take quite a while to perform and will lock the table while it's running, so do it only when the CC is not working.

**5. Use a large key buffer size**


On modern, multi-gigabyte servers there is no need to have a key_buffer_size of 8 megs! Make it at least twice of the key size for the queue_log table, like 128M or 256M

See: [Tuning Server Parameters](http://dev.mysql.com/doc/refman/5.0/en/server-parameters.html)


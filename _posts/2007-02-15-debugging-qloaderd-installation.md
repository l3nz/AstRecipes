---
layout: post
title:  Debugging Qloaderd installation
date:   2007-02-15 17:06:26.0
tags:
 - QueueMetrics
 - qloaderd
 - MySQL

categories: update
---


..to be completed....

**Checking the database can be written to**



**Checking if data is present in the database**


1. connect to the QueueMetrics database using a MySQL shell or GUI
2. issue the following query:

    
    select partition, queue, count(*) as n_records
    from queue_log
    group by partition, queue
    order by partition, queue


The result should look something like:

    
    +-----------+------------------+-----------+
    | partition | queue            | n_records |
    +-----------+------------------+-----------+
    | P003      | myqueue          |         9 |
    | P003      | NONE             |       121 |
    | P003      | queue-abc        |      2096 |
    | P003      | queue-test       |      1341 |
    | P003      | UNK              |        17 |
    | P01       | qq-group         |     33000 |
    | P01       | cust-rajax       |       204 |
    | P01       | NONE             |      8139 |
    | rt        | NONE             |      8064 |
    | rt        | q1               |      9216 |
    | rt        | q2               |      9216 |
    +-----------+------------------+-----------+
    11 rows in set (0.16 sec)


This report shows:

* That we are using three distinct partitions: *P003*, *P01* and *rt*. You will at first find only one.

* For each partition, we see the Asterisk queue names involved plus the special keyword *NONE*

* For each queue, we get an idea of how many records it generated, i.e. how big it was. As a rough estimate, consider that each call generates an average of around three records.

**Checking all data is in the database**


If you want a breakdown by day of the contents of the each partition, you can run the following query:
    
    SELECT partition, FROM_UNIXTIME(time_id, '%Y%m%d' ) as date, count(*) as n_rows 
    FROM queue_log 
    GROUP BY partition, FROM_UNIXTIME( time_id, '%Y%m%d' )
    ORDER BY partition, FROM_UNIXTIME( time_id, '%Y%m%d' );


The result will look something like:

    
    +-----------+----------+--------+
    | partition | date     | n_rows |
    +-----------+----------+--------+
    | P01       | 20070329 |   4216 |
    | P01       | 20070411 |      5 |
    | P01       | 20070412 |      3 |
    | rt        | 20070508 |   9365 |
    | rt        | 20070509 |  13248 |
    | rt        | 20070510 |   3883 |
    +-----------+----------+--------+
    6 rows in set (0.45 sec)





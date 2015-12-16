---
layout: post
title:  QueueMetrics user tracking
date:   2007-05-08 17:40:13.0
tags:
 - QueueMetrics
 - user
 - tracking

categories: update
---

To track your users loggin on and off of QueueMetrics, have a look at *localhost-xxxx.txt* under *tomcat/logs*, you should find something that looks like the following examples.

The value in square brackets (that we edited for brevity) is the session-id, it stays the same throughout the session.
All entries havinbg the same session-id are for the same user from the same browser.

This is for a log-on:

    
    [EFDC....] Inizio classe it.loway.app.queuemetrics.validazione.logon
    [EFDC....] Fine classe it.loway.app.queuemetrics.validazione.logon
    [EFDC....] Utente 'demoadmin' in classe '3'
    [EFDC....] Utente 'demoadmin' ha chiavi 'USER QUEUE_AN ...'
    [EFDC....] Inizio classe it.loway.app.queuemetrics.autenticazione.caricaDatiIniziali
    [EFDC....] Fine classe it.loway.app.queuemetrics.autenticazione.caricaDatiIniziali
    [EFDC....] Inizio classe it.loway.app.queuemetrics.oq.caricaCodeOutbound
    [EFDC....] Fine classe it.loway.app.queuemetrics.oq.caricaCodeOutbound
    [EFDC....] Tempo totale esecuzione verbo 'qm_autentica': 91 ms


The entry "Utente 'demoadmin' in classe '3'" means that a user called "demoadmin" just logged on.

This is for a log off:

    
    [EFDC....] Utente  sconnesso.
    [EFDC....] Tempo totale esecuzione verbo 'qm_logoff': 14 ms


This way you can track what your users have been doing and who logged on to the system.


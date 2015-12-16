---
layout: post
title:  Upgrading XC-AST licences
date:   2005-04-25 15:06:24.0
tags:
 - QueueMetrics
 - xc-ast
 - licence

categories: update
---

First, you have to ask for a valid XC-AST key. 

Once you have it, to insert the key into XC-AST, you have to: 

* locate the file WEB-INF/web.xml within the XC-AST webapp 

* edit the file with a text editor 

* locate the section with the licence, looking like 
    
    <init-param>
    <param-name>LICENZA_ARCHITETTURA</param-name>
    <param-value>...........</param-value>
    </init-param>



* insert your licence key within the param-value tag, all on one line, exactly as it was sent to you 

* save 

* restart your servlet container 

* try and login to XC-AST using your browser

* press the "Licence" button to see your current licence.


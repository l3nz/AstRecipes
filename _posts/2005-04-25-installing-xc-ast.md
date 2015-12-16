---
layout: post
title:  Installing XC-AST
date:   2005-04-25 15:10:05.0
tags:
 - xc-ast
 - installing

categories: update
---

To install XC-AST, you need: 

* Java 1.4+ working on your machine (if you don't, download a Java SDK from Sun) 

* A modern JSP/servlet container, like Apache Tomcat 5 or Jetty 

*MySQL version 4 

These components should be already working before attemping an installation. As these components are quite OS independent, the software should work on most modern operating systems; we have tried it on both a Win32 system and on various flavours of Linux. 

If you are using Jetty as the servlet container, there is a [detailed recipe here]({% post_url 2005-04-25-installing-xc-ast-under-jetty %}). 

**Installing**


* Unpack the XC-AST tar archive, look for instructions within xc-ast/WEB-INF/README 

* Deploy the files in the webapp area of your servlet container 

* Download third party modules (see [Adding third-party modules to XC-AST]({% post_url 2005-04-25-adding-third-party-modules-to-xc-ast %})) 

* Create the required database (see [Creating XC-AST database]({% post_url 2005-04-25-creating-xc-ast-database %})). 

* Start or restart your servlet container 

* Point your browser to http://YOURSERVER/xc-ast/ 

* After you log in, not forget to change the users passwords!


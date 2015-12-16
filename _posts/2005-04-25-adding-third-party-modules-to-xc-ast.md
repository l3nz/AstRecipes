---
layout: post
title:  Adding third-party modules to XC-AST
date:   2005-04-25 15:13:00.0
tags:
 - xc-ast
 - jar

categories: update
---

XC-AST requires three third party libraries to start correctly. If these libraries are not placed within the WEB-INF/lib directory of XC-AST, the application will likely not start. 

To download these component, you have to visit their web site and usually download an archive that contains the .jar file. Only the JAR fileitself needs to be placed within WEB-INF/lib. 

These are the required components: 

* mail.jar - see [http://java.sun.com/products/javamail/](http://java.sun.com/products/javamail/)

* activation.jar - see [http://java.sun.com/products/javabeans/glasgow/jaf.html](http://java.sun.com/products/javabeans/glasgow/jaf.html)

* mysql-connector-java-3.0.10-stable-bin.jar - see [http://www.mysql.com/products/connector-j/index.html](http://www.mysql.com/products/connector-j/index.html)

Please note that it is important that you download the exact versions, otherwise the might not work at all.



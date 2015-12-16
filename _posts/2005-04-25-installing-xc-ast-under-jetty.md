---
layout: post
title:  Installing XC-AST under Jetty
date:   2005-04-25 15:09:12.0
tags:
 - xc-ast
 - Jetty

categories: update
---

This procedure works the same way with both Jetty 4.x or Jetty 5.x. 


* Download Jetty 5.0 from this link. 

* Unpack the Jetty archive and change working dir to Jetty-5.0.0 

* Locate the file etc/demo.xml and copy it to to etc/xcast.xml 

* Edit etc/xcast.xml adding the following lines where you find a lot of similar entries: 
    
    <Call name="addWebApplication">
      <Arg>/xc-ast/*</Arg>
      <Arg><SystemProperty name="jetty.home" default="."/>/webapps/xc-ast/</Arg>
    </Call>



* Unpack xc-ast within the directory webapps; rename the unpacked directory xc-ast (it is usually something like xc-ast-0.6.0-eval) 

* Add the required jars (see [Adding third-party modules to XC-AST]({% post_url 2005-04-25-adding-third-party-modules-to-xc-ast %})) and create the required database (see [Creating XC-AST database]({% post_url 2005-04-25-creating-xc-ast-database %})). 

* Start jetty with java -jar start.jar etc/xcast.xml 

* Point your browser to http://127.0.0.1:8080/xc-ast if you are on the same Linux box, or http://my_server_address:8080/xc-ast if you are not .

There you are!

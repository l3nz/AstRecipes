---
layout: post
title:  Creating XC-AST database
date:   2005-04-25 15:11:54.0
tags:
 - xc-ast
 - MySQL

categories: update
---

You need to have a recent version of MySQL installed. 

* find the file log_code_web.sql that is contained within WEB-INF/README in the XC-AST distribution 

* create a database to upload it, the following instructions will usually do:
    
    mysql mysql
    create database test_xcast;
    grant all privileges on test_xcast.* to 'myuser'@'localhost' identified by 'mypassword';
    exit



* Upload test data
    
    mysql --user=myuser --password=mypassword test_xcast < log_code_web.sql 



* Update web.xml to set your database configuration
Edit the file WEB-INF/web.xml and edit the JDBC_URL in the following way:
    
    <init-param>
    <param-name>JDBC_URL</param-name>
    <param-value>jdbc:mysql://localhost/test_xcast?user=myuser&password=mypassword</param-value>
    </init-param>



* Restart (or start) your servlet container



After following these instructions I received  

Error message:

The reference to entity "password" must end with the ';' delimiter.

This error is caused by a rogue '&' in web.xml. This xml tag...

<param-value>jdbc:mysql://localhost/test_xcast?user=myuser&password=mypassword</param-value>

should actually read...

user=myusername&amp;password=mypassword


This is because the sax xml parsing libraries used by jwnl are generic xml libraries and therefore expect an escaped charcter sequence.





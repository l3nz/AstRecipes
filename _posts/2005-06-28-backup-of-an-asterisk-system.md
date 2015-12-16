---
layout: post
title:  Backup of an Asterisk system
date:   2005-06-28 12:47:22.0
tags:
 - #frontpage
 - backup

categories: update
---

This quick and dirty shell script will backup the most important stuff of an Asterisk installation and create one big file made up of many smaller files.

I assume you have an Asterisk box with a quite standard installation path and XC-AST installed too (therefore you want to backup Tomcat and MySQL).

    
    ora=`date '+%Y.%m.%d-%H.%M.%S'`
    
    tar zcf etc-asterisk-$ora.tar.gz /etc/asterisk
    tar zcf var-log-asterisk-$ora.tar.gz /var/log/asterisk
    tar zcf var-lib-asterisk-$ora.tar.gz /var/lib/asterisk
    tar zcf var-spool-asterisk-$ora.tar.gz /var/spool/asterisk
    tar zcf usr-lib-asterisk-$ora.tar.gz /usr/lib/asterisk
    
    
    tar zcf var-lib-mysql-$ora.tar.gz /var/lib/mysql
    tar zcf usr-local-tomcat-$ora.tar.gz /usr/local/tomcat/webapps
    
    
    # make one big file of them all
    
    tar zcf asterisk-stuff-$ora.tgz *$ora*
    rm -f $ora.tar.gz



If Tomcat and MySQL are not installed, you need not backup /var/lib/mysql and /usr/local/tomcat/webapps


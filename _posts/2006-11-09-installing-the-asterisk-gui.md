---
layout: post
title:  Installing the Asterisk GUI
date:   2006-11-09 17:27:08.0
tags:
 - GUI
 - compiling
 - Asterisk
 - #frontpage

categories: update
---

Asterisk 1.4 includes an HTML Asterisk GUI (well, it's more of a package for the creation of GUIs) by Digium. Installing it is quite easy once you have installed Asterisk 1.4 itself - see node [Compiling Asterisk 1.4 beta]({% post_url 2006-11-09-compiling-asterisk-1.4-beta %}).

The GUI is a nice AJAX app that will run through the Asterisk embedded web server with a recent web browser.

The first thing you'll have to do is to download the GUI, currently through Subversion only:

    
    svn checkout http://svn.digium.com/svn/asterisk-gui/trunk asterisk-gui


don't worry if it takes a while to get this started, just let it run until the package is downloaded. 

    
    cd asterisk-gui/
    make
    make install
    make samples


Before the package can start, you'll have to change a few things in your Asterisk:


* Enabling the embedded HTTP manager and webserver
Edit */etc/asterisk/http.conf* and change the following lines:

    
    enabled=yes
    enablestatic=yes
    bindaddr=0.0.0.0


This enables the HTTP server, enables it to serve static content (the GUI itself) and binds to the standard address, so that any browser from a remote machine can access it.



* Configuring the manager access
Edit */etc/asterisk/manager.conf* and change the following lines:

    
    enabled = yes
    webenabled = yes


Then add a new section at the end of the file

    
    [admin]
    secret = test
    read = system,call,log,verbose,command,agent,user,config
    write = system,call,log,verbose,command,agent,user,config


This will allow a user called *admin* to connect with the password *test* and do nearly everything on the system.

Now restart Asterisk.

Do one last pre-flight test with the command 

    
    make checkconfig


If everything goes well, connect with a recent browser to:

    
    http://10.10.3.103:8088/asterisk/static/config/cfgbasic.html


Where 10.10.3.103 is the address of your Asterisk system, and login as *admin* password *test*. You did it! The Asterisk GUI is now correctly configured.



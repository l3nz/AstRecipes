---
layout: post
title:  Tunnelling with PuTTY
date:   2008-01-18 14:42:45.0
tags:
 - unix
 - SSH
 - QueueMetrics
 - PuTTY

categories: update
---

When accessing a remote Unix system from a windows machine, it is very common to be given shell access via SSH. It is also very common to need being forwarder port 80 (for web configuration of the system) and port 8080 (for QueueMetrics); and this is a hassle to set up for remote system administrators who need to forward not one but three ports on their firewall.

By using PuTTY, it is possible to enable "tunnelling", so that one or more remote ports (endpoints) are connected to some other ports on the local machine.

To do this, open up PuTTY and select :


* **Connection -> SSH**

Set *Enable compression: yes* and *Preferred SSH version: 2* (the latter is not necessary, but it's better security)


* **Connection -> SSH -> Tunnels**

We want to connect the local port 9090 to port 8080 of the remote server.
Go to the *Add new forwarded port* and enter: *Source port: 9090* and *Destination: 127.0.0.1:8080*. Press *Add*.
This means that the remote SSH tunnel will try to connect to *127.0.0.1:8080* and that will be teleported to the loacl machine's port 9090. 
You can add as many ports as you like this way.


**See also**


* [PuTTY home page](http://www.chiark.greenend.org.uk/~sgtatham/putty/)

>>Thanks to Richard B. for input!<<



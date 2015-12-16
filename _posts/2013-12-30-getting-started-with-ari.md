---
layout: post
title:  Getting started with ARI
date:   2013-12-30 15:07:50.0
tags:
 - Stasis
 - ARI
 - #popular
 - #frontpage

categories: update
---

Setting up the Asterisk REST Interface on an Asterisk 12 system for an introductory test-drive is quite straightforward. The idea behind ARI is that you have a RESTful part where you send commands and a websocket to receive events.

**Asterisk configuration**


Edit */etc/asterisk/http.conf* so that:

    
    [general]
    enabled=yes
    bindaddr=0.0.0.0
    bindport=8088


And then create an ARI user in */etc/asterisk/ari.conf*:

    
    [general]
    enabled = yes
    
    [aritest]
    type = user
    read_only = no
    password = testme
    password_format = plain


This creates a new user called *aritest* with password *testme*.

Now have Asterisk reload and test that the configuration was picked up.

    
    localhost*CLI> reload
    ...
    localhost*CLI> ari show users
    r/o?  Username
    ----  --------
    No    aritest
    localhost*CLI> ari show status
    ARI Status:
    Enabled: Yes
    Output format: compact
    Auth realm: Asterisk REST Interface
    Allowed Origins:
    User count: 1


**Testing ARI - the websocket**


You can install the *wscat* tool to test the ARI. It is a part of the EPEL repository that, though not technically a part of CentOS 6, it surely complements it (see [https://fedoraproject.org/wiki/EPEL](https://fedoraproject.org/wiki/EPEL) if you do not have it available).

    
    yum install nodejs nodejs-options nodejs-commander
    yum install nodejs-ws



Then type:

    
    # wscat --connect 'ws://localhost:8088/ari/events?app=hello&api_key=aritest:testme'
    connected (press CTRL+C to quit)


If so far everything is okay, we can now run a test that is a little more meaningful.

First edit your */etc/asterisk/extensions.conf* and add the following stanza:

    
    [testari]
    exten => 1,1,Noop()
    same => n,Stasis(hello,world)
    same => n,Hangup()


This just creates a new context that sends calls to the Stasis (ARI) app called "hello" with a parameter of "world".

We reload Asterisk so that the dialplan is picked up and in  a new window we run again the *wscat* command above.

While *wscat* is running, go to the Asterisk CLI and type:

    
    localhost*CLI> channel originate Local/1@testari application wait 100


On the websocket window you'll see:

    
    < {
      "type": "StasisStart",
      "timestamp": "2013-12-30T15:21:45.688+0100",
      "args": [
        "world"
      ],
      "channel": {
        "id": "1388413305.5",
        "name": "Local/1@testari-00000002;2",
        "state": "Ring",
        "caller": {
          "name": "",
          "number": ""
        },
        "connected": {
          "name": "",
          "number": ""
        },
        "accountcode": "",
        "dialplan": {
          "context": "testari",
          "exten": "1",
          "priority": 2
        },
        "creationtime": "2013-12-30T15:21:45.688+0100"
      },
      "application": "hello"
    }
    
    < {
      "type": "StasisEnd",
      "timestamp": "2013-12-30T15:22:15.716+0100",
      "channel": {
        "id": "1388413305.5",
        "name": "Local/1@testari-00000002;2",
        "state": "Ring",
        "caller": {
          "name": "",
          "number": ""
        },
        "connected": {
          "name": "",
          "number": ""
        },
        "accountcode": "",
        "dialplan": {
          "context": "testari",
          "exten": "1",
          "priority": 2
        },
        "creationtime": "2013-12-30T15:21:45.688+0100"
      },
      "application": "hello"
    }


(Ok I turned on pretty printing in ari.conf in order to make this readable).

**Testing ARI - the RESTful part**


Running REST commands through the interface is very straightforward:

    
    [root@localhost ~]# curl -v -u aritest:testme -X GET "http://localhost:8088/ari/asterisk/info"
    * About to connect() to localhost port 8088 (#0)
    *   Trying 127.0.0.1... connected
    * Connected to localhost (127.0.0.1) port 8088 (#0)
    * Server auth using Basic with user 'aritest'
    > GET /ari/asterisk/info HTTP/1.1
    > Authorization: Basic YXJpdGVzdDp0ZXN0bWU=
    > User-Agent: curl/7.19.7 (i386-redhat-linux-gnu) libcurl/7.19.7 NSS/3.14.0.0 zlib/1.2.3 libidn/1.18 libssh2/1.4.2
    > Host: localhost:8088
    > Accept: */*
    >
    < HTTP/1.1 200 OK
    < Server: Asterisk/12.0.0
    < Date: Wed, 01 Jan 2014 13:20:15 GMT
    < Connection: close
    < Cache-Control: no-cache, no-store
    < Content-Length: 535
    < Content-type: application/json
    <
    {
      "build": {
        "os": "Linux",
        "kernel": "2.6.32-431.el6.i686",
        "machine": "i686",
        "options": "LOADABLE_MODULES, OPTIONAL_API",
        "date": "2013-12-30 13:25:41 UTC",
        "user": "root"
      },
      "system": {
        "version": "12.0.0",
        "entity_id": "52:54:00:26:9d:07"
      },
      "config": {
        "name": "",
        "default_language": "en",
        "setid": {
          "user": "",
          "group": ""
        }
      },
      "status": {
        "startup_time": "2013-12-30T14:30:39.344+0100",
        "last_reload_time": "2013-12-30T15:21:39.354+0100"
      }
    * Closing connection #0


You'll have to look up each command - so better using a library.


**See also:**

* [Getting started with ARI](https://wiki.asterisk.org/wiki/display/AST/Getting+Started+with+ARI) on which this tutorial is based
* [The ari4java library](https://github.com/l3nz/ari4java) wraps ARI calls so that it is accessible from Java.




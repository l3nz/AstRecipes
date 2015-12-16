---
layout: post
title:  Peering two Asterisk servers using IAX
date:   2006-10-04 18:58:44.0
tags:
 - peering
 - IAX
 - #frontpage

categories: update
---

It is quite easy to "join together" two Asterisk server using IAX. Unfortunately it takes a while to get things right by reading the docs, so here is a ready-made recipe that you can use to have two Asterisk box dial each others extensions.

We imagine that we have two Asterisk boxen, called *foo* and *bar*. *foo* is at 192.168.1.2 while *bar* is at 192.168.2.3. Le's start with *foo*; here is its *iax.conf* file:

    
    [general]
    bindport = 4569           ; Port to bind to (IAX is 4569)
    bindaddr = 0.0.0.0    ; Address to bind to (all addresses on machine)
    disallow=all
    allow=ulaw
    allow=alaw
    allow=gsm
    mailboxdetail=yes
    
    
    [bar]
    type=friend
    username=foo
    secret=password123
    auth=plaintext
    host=192.168.2.3
    context=fromiax
    peercontext=fromiax
    qualify=yes
    trunk=yes


This configuration means that on *foo* we can do *Dial(iax2/bar/23)* and this will call extension 23 at context *fromiax* on *bar*.

Here is the meaning of the options:

* We use *bar* as the name of the section so we cal dial *iax2/bar* and we add login and password so we don't need to remember them in the dialplan

* We define the **type**
 as *friend*, meaning that we will both send and receive calls to *bar*. This is the same as defining both *user* and *peer* sections, but does save time

* The **username**
 is the name of the *iax.conf* section on *bar* where we will look for IAX peering information; the **secret**
 is the connection password. Use the same password for both sides of the peering, if you are using *type=friend*! The **auth**
 is the kind of authentication performed; you may want to use safer methods that sending plaintext passwords as in this case, like setting it to md5.

* The **host**
 is the IP address of the server *bar*. As we specify IP addresses, no need for a separate registration

* The **context**
 is the context in which we will accept the extension *bar* will be sending us when *bar* tries to call us. We can have more than one possible context line for *bar*, so they can choose in which context they want to call us.

* The **peercontext**
 is used to avoid typing in the context in the Dial() command, so that we can write the easier dial string  *iax2/bar/23* instead of *iax2/bar/23@fromiax*

* The **qualify**
 option generates a bit of 'pings' between the servers, so they make sure the other part is ready

* The **trunk**
 option makes sure that if more than one call is travelling between the servers, they will be sent in a way that saves quite a bit of IP overhead.

Now we build a very similar configuration for the other server, *bar*:

    
    [general]
    bindport = 4569           ; Port to bind to (IAX is 4569)
    bindaddr = 0.0.0.0    ; Address to bind to (all addresses on machine)
    disallow=all
    allow=ulaw
    allow=alaw
    allow=gsm
    mailboxdetail=yes
    
    
    [foo]
    type=friend
    username=bar
    secret=password123
    auth=plaintext
    host=192.168.1.2
    context=fromiax
    peercontext=fromiax
    qualify=yes
    trunk=yes


As you can see, we made specular changes in the section name and username and host, while keeping the same password.

Of course, on both servers we have to create a context named *fromiax* in extensions.conf that acts as a 'landing zone' for incoming calls from the other server.

Now on *foo* we can *Dial(iax2/bar/23)* to reach extension 23@fromiax on *bar*, and on *bar* we can dial *Dial(iax2/foo/42)* to reach extension 42@fromiax on *foo*. 

That was easy, wasn't it?



**See also:**


* [http://blog.thegoldfish.net/dundi-tutorial-for-asteriskhome/](http://blog.thegoldfish.net/dundi-tutorial-for-asteriskhome/)

* [http://kneh.xs4all.nl/tijmen/asterisk/Using%20DUNDi%20with%20Trixbox.pdf](http://kneh.xs4all.nl/tijmen/asterisk/Using%20DUNDi%20with%20Trixbox.pdf)

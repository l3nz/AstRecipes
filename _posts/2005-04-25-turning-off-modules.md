---
layout: post
title:  Turning off modules
date:   2005-04-25 15:03:01.0
tags:
 - modules

categories: update
---

t might happen that you need to turn off modules you are not currently using or not working on your Asterisk box. 

You can edit /etc/asterisk/modules.conf in the <[>Modules<](-NOTFOUND->Modules<)> to tell Asterisk not to load existing modules; for example, the following example we do not load modules for the modem: 

noload => chan_modem.so
noload => chan_modem_aopen.so
noload => chan_modem_bestdata.so
noload => chan_modem_i4l.so

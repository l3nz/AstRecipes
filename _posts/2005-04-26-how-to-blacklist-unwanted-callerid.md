---
layout: post
title:  How to blacklist unwanted callerid
date:   2005-04-26 02:36:01.0
tags:
 - extensions
 - blacklist
 - #frontpage

categories: update
---

First add extension to allow blacklisting from the phone. You may renumber *96 to whatever you want to not conflict with other extensions on your system.

    
    [app-system-blacklist]
    ; *96 <xxxxxxxxxx> 1 - black list phone number <xxxxxxxxxx> to level <y>
    ; *96 <xxxxxxxxxx>   - Delete system speed dial <xxxxxxxxxx>
    
    exten => _*96XXXXXXXXXX.,1,Answer
    exten => _*96XXXXXXXXXX.,2,DBput(blacklist/${EXTEN:3:10}=${EXTEN:13})
    exten => _*96XXXXXXXXXX.,3,Wait(1)
    exten => _*96XXXXXXXXXX.,4,AGI(festival-script.pl|phone number)
    exten => _*96XXXXXXXXXX.,5,SayDigits(${EXTEN:3:10})
    exten => _*96XXXXXXXXXX.,6,AGI(festival-script.pl|has been blacklisted at level)
    exten => _*96XXXXXXXXXX.,7,SayDigits(${EXTEN:13})
    exten => _*96XXXXXXXXXX.,8,Hangup
    
    exten => _*96XXXXXXXXXX,1,Answer
    exten => _*96XXXXXXXXXX,2,DBdel(blacklist/${EXTEN:3:10})
    exten => _*96XXXXXXXXXX,3,Wait(1)
    exten => _*96XXXXXXXXXX,4,AGI(festival-script.pl|blacklisted phone number)
    exten => _*96XXXXXXXXXX,5,SayDigits(digits/${EXTEN:3:10})
    exten => _*96XXXXXXXXXX,6,AGI(festival-script.pl|has been cleared)
    exten => _*96XXXXXXXXXX,7,Hangup


then add these lines and number accordingly before the answer command to your incoming extension

    
    exten => s,?,Zapateller(answer|nocallerid)
    exten => s,?,PrivacyManager
    exten => s,?,LookupBlacklist
    exten => s,?,LookupCIDName

regular part of the incoming extension and then
    
    exten => s,102,Macro(blacklisted1)
    exten => s,103,Macro(blacklisted1)
    exten => s,105,Macro(blacklisted1)
    exten => s,107,Macro(blacklisted1)


then you need to add the blacklisted1 macro

    
    [macro-blacklisted1]
    exten => s,1,Answer
    exten => s,2,Wait(1)
    ;exten => s,3,Zapateller(answer|nocallerid)
    exten => s,3,Playback(sit)
    exten => s,4,Playback(ss-noservice)
    exten => s,5,Hangup 


get the sit sound file from 
[http://sourceforge.net/tracker/download.php?group_id=123387&atid=696352&file_id=130850&aid=1182658](http://sourceforge.net/tracker/download.php?group_id=123387&atid=696352&file_id=130850&aid=1182658)

and put that in your sound folder

then reload  asterisk to get the new configuration
now pickup a phone and key in *96 then the 10 digit number of your cell phone then a 1 to black list your cell phone
then call your system and see if you get the sit sound and then the ss-noservice sound and then hangup

if it works you are done

then pickup your phone and key in *96 and the 10 digit number you want to remove from the blacklist.





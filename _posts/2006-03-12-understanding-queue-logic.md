---
layout: post
title:  Understanding queue logic
date:   2006-03-12 13:25:11.0
tags:
 - queue
 - timeout
 - #frontpage

categories: update
---

When a call enters the queue application, there are many possible scenarios, that are controlled by the queue's own timeout and the ringing timeout.

If a timeout is specified on the queue command itself, that is the maximum queue wait length (we call this *queue timeout*). It is the maximum time that a caller can wait on hold on the queue. If that time is expired, then the caller is moved on to the next step in the dialplan. If no timeout is specified, we assume it to be the default value, i.e. 300 seconds (5 minutes).

There is then another timeout specified in the queue definition itself, that is the *ringing timeout*. This is the queue "loop time", i.e. the timeout over which events in the queue are rotated. If the queue finds a free member, it tries to ring it for the given period of time. If it finds no free members, it retries finding a new member when this time period expires. In any case, the global queue timeout is triggered only when this timer expires, so if this timeout is very long, your queue timeouts may be longer than expected. If a member is busy or unavailable (e.g. an extension that is not working) it is skipped as soon as the condition is detected and the next available agent is rung.

If the option "n" is passed to the queue command, there are no retries, i.e. when the retry period expires, the queue is terminated no matter what. 

It is possible to add a wait time after an extension is ringed unsuccessfully and the next one is ringed; this parameter id the *retry* parameter in the queue definition.

When the ringing timeout expires, all queue events are processed, not only the global queue timeout; if an announce is set and the time from the last announce has elapsed, then the announce is played. While the announce is being played, no new agent is being called; this might be a minor problem if your *queue-thankyou* message is very short, but if it is quite long and quite frequent, you may find an undue slowdown on your agents being searched. 

As a summary, we can say that:

* Your ringing timeout should ideally be quite short (10-15 seconds)

* Announce frequencies and the overall queue timeout should be comparatively much longer than the ringing timeout, and are triggered on a schedule driven by the ringing timeout

* The time taken to play announces is added between each ringing timeout cycle, so if it is very long, you may find that your agents sit idle while callers listen to the announcement.







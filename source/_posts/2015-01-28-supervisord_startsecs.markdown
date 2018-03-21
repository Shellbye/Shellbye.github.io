---
author: shellbye
comments: true
date: 2015-01-28 11:27:08+00:00
layout: post
slug: supervisord-startsecs
title: supervisord startsecs
categories:
- tech_world
tags:
- python
- supervisord
---

[Supervisord](http://supervisord.org/index.html) is really a good tool. But the doc is a little confused. I recently encountered a problem like this:

I have the supervisord.conf file like contains this:

    [program:my_program]
    command=python my_program.py
    process_name=%(program_name)s
    numprocs=1
    directory=%(here)s
    umask=022
    priority=999
    autostart=true
    autorestart=true
    startsecs=3

And when check the stat through http://127.0.0.1:9001, the program will be fatal after a few seconds and the page says:

    Exited too quickly (process log may have details)

When I check the log, it says:

    2015-01-28 10:44:08,587 INFO gave up:  my_program entered FATAL state, too many start retries too quickly

I can't find anything useful from the log. After some quick search and I find [this](https://github.com/Supervisor/supervisor/issues/212)
It's turned out the the `startsecs` means not what I understand like `need how long to start` but means `at least run so long`.
So I set `startsecs` to 1, and all worked fine.
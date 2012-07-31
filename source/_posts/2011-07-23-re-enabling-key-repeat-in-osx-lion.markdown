---
comments: true
date: 2011-07-23 22:51:15
layout: post
slug: re-enabling-key-repeat-in-osx-lion
title: Re-enabling Key Repeat in OSX Lion
wordpress_id: 37
categories:
- Apple
- Technology
tags:
- key-repeat
- lion
- mac
- osx
---

I have recently upgraded to OSX Lion, and I have to say that I love everything about it. Except for one thing. In many apps, the key repeat has been disabled in favor of the new press-and-hold popup for getting alternative characters.

This is fine for most apps, but for apps like [PyCharm](http://www.jetbrains.com/pycharm/) where I use vi key maps, it becomes very, very frustrating.

I came across this little tip to re-enable the key repeat, and my life is measurably better (first world problems, I know...).

Run the following in your Terminal.app:



```
$ defaults write -g ApplePressAndHoldEnabled -bool false
```


Then reboot, and you should be good to go!

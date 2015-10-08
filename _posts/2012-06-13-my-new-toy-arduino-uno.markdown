---
author: admin
comments: true
date: 2012-06-13 15:56:03+00:00
layout: post
slug: my-new-toy-arduino-uno
title: 我的新玩具——Arduino Uno
wordpress_id: 891
categories:
- Linux
---

最新入了块Arduino Uno板子，windows下三下两下搞定了开发环境，arch下倒是费了不少时间。

基本过程参考的是Arch的wiki：[https://wiki.archlinux.org/index.php/Arduino](https://wiki.archlinux.org/index.php/Arduino) 这里就不废话了

几点：

0，最近的filesystem包升级，让/run/lock的权限不是777而是644了，如果arduino里选不了端口（Tools->Serial Port是灰色的），可以这样:


    
    #chmod 777 /run/lock



1，我现在都没把wiki中的向ttyACM0设备echo字符搞出来，我放弃了。反正Examples里的strings相关例子我这里都是成功的。

2，一个arduino 1.0.1可能没有解决的bug，如果出现需要降级avr-libc到1.7.1


    
    #pacman -U http://fs.mupuf.org/mupuf/arduino/avr-libc-1.7.1-1-any.pkg.tar.xz



详情在这里：[http://code.google.com/p/arduino/issues/detail?id=795](http://code.google.com/p/arduino/issues/detail?id=795)

3，<del>一个目前没有解决的问题，如果各位谁知道怎么做千万告诉我：</del>无法以普通用户身份打开/dev/ttyACM0,所以现在各种相关操作包括启动arduino的IDE都要以root身份，这不科学-_-!
感谢intijk的提醒，diff了一下root跟普通用户的 ~/.arduino/preference.txt, 将普通用户的serial.debug_rate改成9600搞定

4，arduino官方ide提供了serial monitor，过去我一般是用minicom连接。刚刚我发现了一个不错的方法,竟然还可以用screen: [http://arduino.cc/playground/Interfacing/LinuxTTY](http://arduino.cc/playground/Interfacing/LinuxTTY)

5，没有5

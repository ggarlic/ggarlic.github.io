---
author: admin
comments: true
date: 2014-03-10 17:01:24+00:00
layout: post
slug: archlinux-on-beaglebone-black
title: Archlinux on Beaglebone Black
wordpress_id: 986
categories:
- Linux
---
@etiont 啊我也加入了自己很鄙视的装系统冒充geek的队伍～

选arch因为它软件新，省下不少为了获得新版本交叉编译的事。

本着不写yet another piece of shit的作风，不赘述安装过程了，这也不是一篇手把手教人安装的文章。直接扔链接:

[http://archlinuxarm.org/platforms/armv7/ti/beaglebone-black](http://archlinuxarm.org/platforms/armv7/ti/beaglebone-black)

至于设置USB Network：

需要加载的模块及i2c修改 [http://archlinuxarm.org/forum/viewtopic.php?f=48&t=5504&sid=843cd57f6fadf0533b2b6d54dc50a860](http://archlinuxarm.org/forum/viewtopic.php?f=48&t=5504&sid=843cd57f6fadf0533b2b6d54dc50a860)

我使用的/etc/netctl/examples/ethernet-static自己进行了修改。netctl用法请自行google，一直都认为这货比垃圾Network Manager好用很多，要是其他发行版也有它多好。

比较完整的过程：[Beaglebone Black Mit Arch Linux ssh Mittels Ethernet Usb ](http://www.klingsiek.at/2014/02/beaglebone-black-mit-arch-linux-ssh-mittels-ethernet-usb/)

上面那篇文章只有电脑与BBB互联的部分。BBB通过USB联网部分需要参考：[Getting Started with BeagleBone using Archlinux](http://recursive-labs.com/blog/2012/06/15/beaglebone-archlinux-gettingstarted/)的TCP/IP Networking - connecting to the Net部分。

系统设置部分，请参考archlinux安装指南

TODO:

crossing compiling using distcc

asterisk

tethering with Mac 妈蛋，一直以为自己的BBB设置有问题，憋了半天，回来插上家里的Arch就能用

HDMI

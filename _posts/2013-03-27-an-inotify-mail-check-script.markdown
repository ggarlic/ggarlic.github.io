---
author: admin
comments: true
date: 2013-03-27 09:08:35+00:00
layout: post
slug: an-inotify-mail-check-script
title: 邮件提醒脚本
wordpress_id: 951
categories:
- Linux
---
主角是inotify，最近在做{ruby,python} koans的时候，被不停切换窗口搞烦了，经人提醒有个监视磁盘的工具inotify-tools。这样就可以在vim保存的同时，统一窗口自动执行测试。毒，德味的，学习了。

正好我用offlineimap收邮件，缺个邮件提醒。bash自带的$mailpath也能用，但是总感觉不直观

{% gist d67cde9e44ac056d50af %}

于是用inotify重写了一下，增加了屏幕提示跟萌妹子声音提醒。inotify太好用了，看man就直接写出来了

{% gist 5252804 %}

但是人的贪欲是无穷的，我有两个邮箱，我想知道到底是哪个邮箱有新邮件，新邮件的发件人跟标题都是什么。

于是就有了下面这个python的版本:

{% gist 5252842 %}

EDIT1: 忘了把测试用的信号改了 IN_CREATE改为IN_MOVED_TO

两个脚本都可以放到awesome的自动启动里，依赖的包自己看着import条目来安装吧

我的邮件配置在[github上的这里](https://github.com/ggarlic/dotfiles)

差点忘了说了，本文第二个作用就是实验gist代码插入功能

呵呵

---
author: admin
comments: true
date: 2014-09-01 04:29:46+00:00
layout: post
slug: range-of-a-semaphore
title: 信号量的范围
wordpress_id: 999
categories:
- Blah
---
之前一直以为pv操作的信号量是可以小于0的，教材也确实是这么讲的。最近发现实际中为了降低复杂度，[实际实现情况并不是这样](http://stackoverflow.com/questions/20656295/what-is-general-semaphores-range)

顺便：看了篇[好玩的文章](http://research.microsoft.com/apps/pubs/default.aspx?id=64242)

讲作者团队用semaphore实现condition variable的过程中出现的种种错误遇到的种种困难

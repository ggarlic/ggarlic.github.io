---
author: admin
comments: true
date: 2015-10-07 15:58:40+00:00
layout: post
slug: python-and-pthread_detach
title: python的pthread_detach
wordpress_id: 1020
categories:
- Blah
---

翻pthread文档，看到pthread_detach，发现好像没在python里见过对应API，翻了翻python源码

https://hg.python.org/cpython/file/v2.7.10/Python/thread_pthread.h#l210

好吧，python的线程生成方法PyThread_start_new_thread里默认就是detach的。可是如果一个线程detach了，怎么能join呢？

然后发现python的join根本就不是封装的pthread_join，https://hg.python.org/cpython/file/v2.7.10/Lib/threading.py#l911

-_-!


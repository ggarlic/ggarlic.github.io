---
author: admin
comments: true
date: 2015-07-12 13:34:19+00:00
layout: post
slug: python-hash-algorithms
title: Python的hash算法
wordpress_id: 1008
categories:
- Blah
tags:
- python
---
之前在组里做[CPython 2.7实现的分享](https://github.com/ggarlic/python-notes)，讲Dict的时候有人问python的hash如何防[hash-flooding attack](http://events.ccc.de/congress/2012/Fahrplan/events/5152.en.html)，真没了解过。

正好刚才看Py3的实现的时候，看到了[PEP-0456](https://www.python.org/dev/peps/pep-0456/)，才知道从3.4开始，为了防范以上的问题，Python已经把自己的hash算法从修改版的[FNV](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function)换乘了[SipHash](https://131002.net/siphash/)

顺便，LWN的一篇文章[Python adopts SipHash](https://lwn.net/Articles/574761/)叙述了Python如何从给FNV打补丁（添加随机前后缀）到最终下决定换成SipHash。

看Python实现代码还是很有收获的：

1，比如学会了[Karatsuba算法](https://en.wikipedia.org/wiki/Karatsuba_algorithm)

2，比如知道了原来那个hash算法是有名字的(孤陋寡闻)

3，比如发现《python源码剖析》里gc部分讲解的bug

另外，python虚拟机做的很多优化，单纯看代码跟实际操作结果不一致，还得去翻虚拟机的优化部分。比如+=跟join，比如intern机制

一不小心，写了一篇slashdot风格的文章。:D

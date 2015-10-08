---
author: admin
comments: true
date: 2014-04-18 10:34:09+00:00
layout: post
slug: twisted-async-web
title: Twisted 异步web
wordpress_id: 993
categories:
- Blah
---
https://gist.github.com/ggarlic/f0d5c4135a98cce96c7a  

访问/2014(其实你可以使用各种年份）返回2014年的日历，访问/async是异步返回ping结果，同时不会阻塞访问/2014跟/async。

访问/sync则同步睡10秒后返回时间戳，会阻塞其他各种请求。

注意：不要使用同一个浏览器来测试。使用curl跟不同浏览器来测试

这其实是一篇吐槽文章。

拿Twisted写web真是无比蛋疼，当然它本来就不是web框架。

Twisted文档看着各种明白，上手写的时候就发现，文档给的例子是狗屁啊，完全没有参考价值。直接去翻别人的代码都比文档好用。







 





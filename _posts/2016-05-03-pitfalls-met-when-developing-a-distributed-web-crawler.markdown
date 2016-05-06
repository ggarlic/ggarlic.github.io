---
author: ggarlic
comments: true
layout: post
slug: pitfalls-met-when-developing-a-distributed-web-crawler
title: Pitfalls Met When Developing a Distributed Web Crawler
categories:
- Develop
---

换组之前留点遗产, 从头设计开发，统一了全公司的爬虫, 设计目标是10亿量级每天，目前的压力测试是已经达标了, 几天前也已上线。在公司同意拿出来公开吹牛逼之前还是不说具体架构了。

先说设计上的一些东西吧。

调度器使用的是 Golang 而不是组里一直用的 Python , 我无意也没兴趣挑起语言争端，选择 golang 是主要是两点考虑：

* 为了想使用静态类型，这样编译器可以帮我分担一些检查工作。
* 调度器需要使用多核并发调度最好打满全部核。虽然 Python 可以多进程搞，但并不自然。

爬虫节点，复用的之前的 Gevent 写的节点，与调度器之间 HTTP 通信。干的就是 Pull 任务-爬-返回结果的活，尽量轻便。碰上一堆坑，后面说。

关于去重，稍微有点经验的人第一反应肯定是 Bloomfilter, 但是10亿这个量级的东西真不该自己开内存做，即使我们换用 Redis 使用 hyperloglog 解决内存问题也得面临不同产品线的 URL 使用的是不同过期时间的问题。最后我们选用了 [Aerospike](http://www.aerospike.com/) 这个 KV 数据库，具体介绍自己点击链接吧。在我们使用之前，已经在公司内另一个项目的去重上使用了有一段时间。 PS：友情吐槽，Python 的 bitmap 模块真够渣的，时不时就漏一下导致 OOM 被 Kill 掉。

然后是坑。

先说爬虫节点的问题，Gevent + Requests, 之前一直有内存泄漏的毛病，一点点漏加上打滚翻倍漏，而且 CPU 居高不下, 搞得云节点痛不欲生
用[火焰图](https://www.nylas.com/blog/performance)试图解决 CPU 问题时，发现程序大量时间浪费在了 decode 页面结果上，即对返回的 Response 调用 text() 方法，这个方法在 Response.encoding 未知的时候，会调用 chardet ，而 chardet 在面对某些字段时会出现内存跟 cpu 暴涨的问题, 具体请看 github 上 [requests 的 issue](https://github.com/kennethreitz/requests/issues/2359) 和 [chardet 的 issue](https://github.com/chardet/chardet/issues/29) 。解决方法，换用 C 实现的 cchardet 或者干脆别解码页面了，我只是个抓取服务而已。火焰图真的是个调优抓 Bug 神器。

解决了这个问题，内存 CPU 暴涨基本差不多了，但是还是会漏，查日志发现这些情况发生在每次抛异常的时候，肯定是 except  代码里有循环引用导致资源不释放，最后发现需要手动 sys.exc\_clear() , 你说这么重要的东西，为什么 Gevent 文档里不写，但是搜 github 上用了 Gevent 的项目大家都这么干呢。

Go 的问题还好，学习成本很低。之前看《C 专家编程》提到 C 的一堆问题，意外发现很多都在 Go 里得到了解决。API 不丰富是个问题，不过我有 Python 标准库可以抄。第三方库数量倒是不是，可是不知道哪家强啊。还有官方 Vendor 出来之前，引用第三方库大家都 github 全路径，万一 github 挂了呢，万一没外网呢，万一引入不兼容的版本升级呢？Go 的 Goroutine 用来开 Worker 简直太方便了。关于 Goroutine 的M:N模型，这货还真不是新东西，正好那段时间在看[《Unix Internals: The New Frontiers》](https://book.douban.com/subject/2037790/), 95年的书， 第三章介绍的轻量线程模型里就有这个。

还有个硬件的坑，压力测试的时候，发现 CPU 俩核打满，但是调度器并没有跑全速，其他核占用也很低, 这应该是网卡收发队列的锅。经过查询，发现装的INTEL 82574网卡竟然不是多队列的，也就没法绑定到多核上了，就是在这种恶劣的条件下达到了调度量目标。坐等公司给换个多队列网卡。

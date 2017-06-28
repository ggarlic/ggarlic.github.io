---
author: ggarlic
comments: true
layout: post
slug: something-about-nginx
title: 关于 nginx 的一些事
categories:
- Develop
---

本文主要是记录最近碰到的几个 Nginx 模块开发时碰到的问题。

### cancelable timer
之前用的是 1.6.x 版本的 nginx，有些年头了。我们的模块会每个四小时从硬盘读取数据缓存内存中。
但是当我们 nginx -s reload 时，旧进程会一直处于 nginx: worker process is shutting down. 几个小时才会彻底消失

模块中设置定时循环读取的代码类似于:

```
static void ngx_data_reload_event_handler(ngx_event_t *ev)
{
    if (ev->timer_set) ngx_del_timer(ev);

    if (!(ngx_quit || ngx_terminate || ngx_exiting)){
        reload_data();
        ngx_add_timer(ev, DEFAULT_RELOAD_INTERVAL*1000);
     }
}

```

究其原因，这样设置的 timer 在进程重启时并没有被删除，需要等到之前设置的时刻到了才会释放，旧进程才能消失。

实际上1.7之后的 Nginx 中，加入了 [cancelable timer](https://trac.nginx.org/nginx/changeset/3efdd7788bb021f09cbe650d49f0b1b1157d08f8/nginx) 的功能。 只需要将 event 设置上 cancelable 属性即可。

```
    ev->cancelable = 1;
```

处理逻辑的位置在 src/os/unix/ngx\_process\_cycle.c 中，相关函数自己去翻吧，我懒得放了。

```
static void ngx_worker_process_cycle(ngx_cycle_t *cycle, void *data)
{
    ngx_int_t worker = (intptr_t) data;

    ngx_process = NGX_PROCESS_WORKER;
    ngx_worker = worker;

    ngx_worker_process_init(cycle, worker);

    ngx_setproctitle("worker process");

    for ( ;; ) {

        if (ngx_exiting) {
            ngx_event_cancel_timers();

            if (ngx_event_timer_rbtree.root == ngx_event_timer_rbtree.sentinel)
            {
                ngx_log_error(NGX_LOG_NOTICE, cycle->log, 0, "exiting");

                ngx_worker_process_exit(cycle);
            }
        }

        ngx_log_debug0(NGX_LOG_DEBUG_EVENT, cycle->log, 0, "worker cycle");

        ngx_process_events_and_timers(cycle);

        if (ngx_terminate) {
            ngx_log_error(NGX_LOG_NOTICE, cycle->log, 0, "exiting");

            ngx_worker_process_exit(cycle);
        }

        if (ngx_quit) {
            ngx_quit = 0;
            ngx_log_error(NGX_LOG_NOTICE, cycle->log, 0,
                          "gracefully shutting down");
            ngx_setproctitle("worker process is shutting down");

            if (!ngx_exiting) {
                ngx_exiting = 1;
                ngx_close_listening_sockets(cycle);
                ngx_close_idle_connections(cycle);
            }
        }

        if (ngx_reopen) {
            ngx_reopen = 0;
            ngx_log_error(NGX_LOG_NOTICE, cycle->log, 0, "reopening logs");
            ngx_reopen_files(cycle, -1);
        }
    }
}

```
### log
今天某无良模块需要写多份日志，调研时无意中发现, 日志函数 ngx_log_error_core (src/core/ngx_log.c) 写日志处调用的 ngx_write_fd 实际上就是个 thin wrapper:

```
/* src/os/unix/ngx_files.h
/*
 * we use inlined function instead of simple #define
 * because glibc 2.3 sets warn_unused_result attribute for write()
 * and in this case gcc 4.3 ignores (void) cast
 */
static ngx_inline ssize_t
ngx_write_fd(ngx_fd_t fd, void *buf, size_t n)
{
    return write(fd, buf, n);
}

```

那么多进程 write 同一个文件是如何保证原子性的呢? 我们知道 Nginx 是通过 O\_APPEND 方式打开日志文件的，Linux 的文件系统是保证 PIP\_BUF(4KiB) 的写操作是原子的。不过, Nginx 的开发者说[更大的值（32KiB）也可以](https://forum.nginx.org/read.php?2,57114,57193#msg-57193)，不知道他们的数据哪来的。


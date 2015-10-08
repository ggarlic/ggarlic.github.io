---
author: admin
comments: true
date: 2015-08-13 06:03:38+00:00
layout: post
slug: term-type-of-tmux-2
title: tmux的term类型
wordpress_id: 1014
categories:
- CLI
---

我iterm上的tmux总是有各种各样的问题，包括mutt排版混乱，weechat排版混乱，vim字体背景颜色从来跟主题颜色不搭。

今天下决心解决了一下，问题出在$TERM上。我的.bashrc中强制指定$TERM为xterm-256color，而tmux要求是screen-256color。虽然我在.tmux.conf中指定了

```
    set -g default-terminal "screen-256color
```

然并卵，启动tmux后$TERM仍然是xterm-256color。

因为tmux启动的时候先读取.tmux.conf中的$TERM设定才会去启动bash，即读取.bashrc，所以.bashrc中的$TERM设定会覆盖tmux的设定。

解决方法也很简单，.bashrc设置$TERM的地方改为：

```
    case "$TERM&" in
         xterm*|rxvt*)
             TERM=xterm-256color
         ;;
    esac
```

即只有在$TERM是xterm的时候才设置$TERM为xterm-256color。

打完收功，work like a charm。

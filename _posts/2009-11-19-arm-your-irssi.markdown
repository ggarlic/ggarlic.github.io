---
author: admin
comments: true
date: 2009-11-19 12:38:44+00:00
layout: post
slug: '%e6%ad%a6%e8%a3%85%e4%bd%a0%e7%9a%84irssi'
title: 武装你的irssi
wordpress_id: 339
categories:
- CLI
tags:
- irssi
- libnotify
---

在[ganquan](http://imganquan.org)的蛊惑下，我又重回irssi，跟xchat相比，irssi确实够轻量的，但是我无法忍受一个没有提醒的IRC客户端

今天下午在在irssi的官方脚本库搜索了一把，装了几个小脚本，基本满足了我的要求

本文所有脚本都是在[irssi.org/scripts](http://irssi.org/scripts)下载

**安装irssi脚本的一般方法**：拷贝到脚本到.irssi/scripts目录下，如果想每次启动时自动启动的话，在scripts下建立autorun目录，在里面建立想自动启动的脚本的软链接,不需要自动启动的脚本可以在运行的时候输入下面的命令来运行

    
    /scripts load XXXX


**先说声音提醒**，beep在我这里总是有这样那样的问题，很是麻烦，我也不想要那个傻乎乎的beep声，我是用了beep.pl，修改倒数第二行，使用自己的播放器和音频文件，我特别别有用心的选择了dog.wav，嘿嘿。

    
    Irssi::settings_add_str("misc", "beep_command",
    "aplay -q /home/ggarlic/.irssi/scripts/dog.wav &");


为了忽略播放器的输出，我将上了quiet参数-q，如果使用mplayer的话，推荐使用-really-quiet这个参数，不然等这输出彻底搞乱你的屏幕吧。

**下面是GUI提醒**，我使用的是notify.pl，并且在irssi中设置

    
    /set beep_msg_level MSGS NOTICES DCC DCCMSGS HILIGHT
    /set beep_when_away ON
    /set beep_when_not_away ON
    /set beep_window_active ON


这样，当有人对你说话或者提到你的时候就会GUI提示

[![大家忽略imganquan的胡扯昂](http://ggarlic.org/blog/wp-content/uploads/2009/11/2009-11-19-1258619473_316x206_scrot-300x195.png)](http://ggarlic.org/blog/wp-content/uploads/2009/11/2009-11-19-1258619473_316x206_scrot1.png)[/caption]

两样东西玩完了，每次ganquan找我的时候我这里就一片狗叫声，哈哈

除此之外，我还装了ascii.pl，这是个好玩的小脚本，不过小心被戴帽子的kick掉你，另外/ascii这个命令需要安装figlet

/ascii /colme /colsay 加上想说的话




[![这个不评论~~~](http://ggarlic.org/blog/wp-content/uploads/2009/11/2009-11-19-1258633510_491x392_scrot1.png)](http://ggarlic.org/blog/wp-content/uploads/2009/11/2009-11-19-1258633510_491x392_scrot1.png)[/caption]





[![我才不信他乱翻呢](http://ggarlic.org/blog/wp-content/uploads/2009/11/2009-11-19-1258633881_355x65_scrot1.png)](http://ggarlic.org/blog/wp-content/uploads/2009/11/2009-11-19-1258633881_355x65_scrot1.png)[/caption]

最后，irssi的基本使用方法可以参考gentoo-cn的wiki，我觉得写的相当不错的，里面也提供了两个脚本，nicklist跟nickcolor，很好用

警告：使用/ascii会造成你的提醒不断想起不断显示，尤其是我这里是狗叫声~~~:D

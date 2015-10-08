---
author: admin
comments: true
date: 2010-04-06 08:58:21+00:00
layout: post
slug: qlocktwo-in-conky
title: QLOCKTWO in Conky
wordpress_id: 598
categories:
- Tips
tags:
- conky
- qlocktwo
---

[![](http://ggarlic.org/blog/wp-content/uploads/2010/04/500x_quicktwo.jpg)](http://ggarlic.org/blog/wp-content/uploads/2010/04/500x_quicktwo.jpg)

请允许我火星一下，这已经是半年多以前的东西。

前两天在crunchbang的论坛里闲逛，发现了有人的conky显示时间的部分是qlocktwo。好奇地google了一下，发现了就是上面图里的东西，太有feel了。此图质量不好，点击[官网](http://www.qlocktwo.com/)看各种色调高清无码图。

想买的童鞋可能要失望了，由于是艺术家的概念产品，这玩意价值$1500，所以，还是等着我朝山寨吧。或者[参考这篇文章](http://www.instructables.com/id/A-Word-Clock/)自己做一个。有水果iphone的童鞋可以去安装这个app。至于我等穷人，没钱，没工具，只好用conky来实现。

－－下面开始说正题，内容来自[crunchbang论坛](http://www.crunchbanglinux.org/forums/topic/4201/qlocktwo-in-conky/)，详情请传送至那里查看－－

先仍张我的效果图馋馋你们:

[![qlocktwo in conky (transparent)](http://farm3.static.flickr.com/2765/4496535410_620dec6988_o.png)](http://www.flickr.com/photos/ggarlic/4496535410/)




    
    own_window yes
    use_xft yes
    xftfont freemono:size=20
    xftalpha .1
    update_interval 15.0
    uppercase yes
    double_buffer yes
    default_color ffffff
    color1 222222
    TEXT
    ${color}I T ${color1}L ${color}I S ${color1}A S T I M E
    ${if_match ${exec date +"%M"} < 35}${if_match ${exec date +"%M"} >= 15}${if_match ${exec date +"%M"} < 20}${color}${endif}${endif}A ${color1}C ${if_match ${exec date +"%M"} >= 15}${if_match ${exec date +"%M"} < 20}${color}${endif}${endif}Q U A R T E R ${color1}D C ${if_match ${exec date +"%M"} >= 20}${if_match ${exec date +"%M"} < 30}${color}${endif}${endif}T W E N T Y ${if_match ${exec date +"%M"} >= 25}${if_match ${exec date +"%M"} < 30}${color}${endif}${else}${color1}${endif}${if_match ${exec date +"%M"} >= 5}${if_match ${exec date +"%M"} < 10}${color}${endif}${endif}F I V E ${color1}X ${if_match ${exec date +"%M"} >= 30}${if_match ${exec date +"%M"} < 35}${color}${endif}${endif}H A L F ${color1}B ${if_match ${exec date +"%M"} >= 10}${if_match ${exec date +"%M"} < 15}${color}${endif}${endif}T E N ${color1}F T O ${else}${if_match ${exec date +"%M"} >= 45}${if_match ${exec date +"%M"} < 50}${color}${endif}${endif}A ${color1}C ${if_match ${exec date +"%M"} >= 45}${if_match ${exec date +"%M"} < 50}${color}${endif}${endif}Q U A R T E R ${color1}D C ${if_match ${exec date +"%M"} >= 35}${if_match ${exec date +"%M"} < 45}${color}${endif}${endif}T W E N T Y ${if_match ${exec date +"%M"} < 40}${if_match ${exec date +"%M"} >= 35}${color}${endif}${else}${color1}${endif}${if_match ${exec date +"%M"} >= 55}${color}${endif}F I V E ${color1}X
    H A L F B ${if_match ${exec date +"%M"} >= 50}${if_match ${exec date +"%M"} < 55}${color}${endif}${endif}T E N ${color1}F ${color}T O${color1}
    ${endif}${if_match ${exec date +"%M"} < 35}${if_match ${exec date +"%M"} >= 5}${color}${endif}${endif}P A S T ${color1}E R U ${if_match ${exec date +"%M"} < 35}${if_match ${exec date +"%I"} == 9}${color}${else}${color1}${endif}N I N E
    ${if_match ${exec date +"%I"} == 1}${color}${else}${color1}${endif}O N E ${if_match ${exec date +"%I"} == 6}${color}${else}${color1}${endif}S I X ${if_match ${exec date +"%I"} == 3}${color}${else}${color1}${endif}T H R E E
    ${if_match ${exec date +"%I"} == 4}${color}${else}${color1}${endif}F O U R ${if_match ${exec date +"%I"} == 5}${color}${else}${color1}${endif}F I V E ${if_match ${exec date +"%I"} == 2}${color}${else}${color1}${endif}T W O
    ${if_match ${exec date +"%I"} == 8}${color}${else}${color1}${endif}E I G H T ${if_match ${exec date +"%I"} == 11}${color}${else}${color1}${endif}E L E V E N
    ${if_match ${exec date +"%I"} == 7}${color}${else}${color1}${endif}S E V E N ${if_match ${exec date +"%I"} == 12}${color}${else}${color1}${endif}T W E L V E
    ${if_match ${exec date +"%I"} == 10}${color}${else}${color1}${endif}T E N ${else}${if_match ${exec date +"%I"} == 8}${color}${else}${color1}${endif}N I N E
    ${if_match ${exec date +"%I"} == 12}${color}${else}${color1}${endif}O N E ${if_match ${exec date +"%I"} == 5}${color}${else}${color1}${endif}S I X ${if_match ${exec date +"%I"} == 2}${color}${else}${color1}${endif}T H R E E
    ${if_match ${exec date +"%I"} == 3}${color}${else}${color1}${endif}F O U R ${if_match ${exec date +"%I"} == 4}${color}${else}${color1}${endif}F I V E ${if_match ${exec date +"%I"} == 1}${color}${else}${color1}${endif}T W O
    ${if_match ${exec date +"%I"} == 7}${color}${else}${color1}${endif}E I G H T${if_match ${exec date +"%I"} == 10}${color}${else}${color1}${endif} E L E V E N
    ${if_match ${exec date +"%I"} == 6}${color}${else}${color1}${endif}S E V E N${if_match ${exec date +"%I"} == 11}${color}${else}${color1}${endif} T W E L V E
    ${if_match ${exec date +"%I"} == 9}${color}${else}${color1}${endif}T E N ${endif}${color1}S E ${if_match ${exec date +"%M"} < 5}${color}${endif}O C L O C K


原帖中还有人将其变成了wallpaper，加入calendar，加入am/pm等等，有兴趣的可以去搞搞。

EOF

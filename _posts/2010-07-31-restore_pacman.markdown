---
author: admin
comments: true
date: 2010-07-31 04:41:27+00:00
layout: post
slug: restore_pacman
title: 恢复/var/lib/pacman
wordpress_id: 702
categories:
- Tips
tags:
- pacman
---

不小心把/var/lib/pacman删除了，别问我为什么，我到现在都不知道我当时脑子怎么抽抽了，大概是热得。

昨晚搞到3点多终于把被删除的玩意都恢复回来了，说说怎么做的吧。

先看看/var/lib/pacman里都是什么东西（恢复后的）：


local/    sync/    archlinuxfr.db.tar.gz    community.db.tar.gz    core.db.tar.gz    extra.db.tar.gz


4个*.db.tar.gz文件显然是源数据库，包含了包的各种基本信息。sync/就是这四个文件解压出来的文件夹。

local/文件夹很重要，本地安装了哪些包，及每个包的各种基本信息都有记录。用local下的grep-2.6.3-1文件夹为例：


grep-2.6.3.1/
|__depends  依赖关系
|__desc  包的描述信息
|__files   包会给系统安装哪些文件
|__install   安装卸载脚本


如果哪天不小心删除了local/的话：



	
  * pacman -Q不会有任何输出，-Syu会提示你系统是最新的

	
  * 安装包的时候，会提示你你一堆已经解决的依赖让你重新安装这些依赖（依赖地狱？）


不过好在有办法恢复~~


## 1，恢复sync/




这个不难，pacman -Syyu就能实现。





## 2,  恢复local/




参考了archlinux的wiki：[restore pacman's local database](http://wiki.archlinux.org/index.php/Pacman_Tips#Restore_pacman.27s_local_database)




我懒得翻译，也懒得用自己的话写出来，点过去自己看吧。**这个方法要用到pacman.log，最好保证这个文件没有被清理过。**文中提供了pacman.log也不能用的时候的解决办法（这人真倒霉=.=）:使用[Xyne的package detection脚本](http://bbs.archlinux.org/viewtopic.php?pid=670876)




至于恢复AUR的包，wiki中首先生成的pkglist.org实际上是包含AUR的包的，sort排序之后跟后面生成的不含AUR包的pkglist对比一下就得出AUR包的列表了：




    
    comm -23 pkglist.org pkglist > pkg.AUR




然后导入安装：




    
    yaourt -S $(< pkglist.AUR)




## 3，杂项




到现在基本差不多了，恢复的时候很浪费时间，不然我也不会折腾到3点才睡了。毕竟是把所有包重新下载一遍，再安装一遍（wiki中的两个方法实际上都是安装了一遍所有的包，只不过一个实际安装，一个是用fakeroot装到了自己定义的${root}下）




都装完了，别忘了-Qdt一下，最好再查查多装了什么包没有，尤其是像我这种有洁癖的人


每一位删除了/var/lib/pacman的人上辈子都是折翼的天使，遇到这样的人你就嫁了吧，这样的人你伤不起。 :orz:

就这样吧。

EOF

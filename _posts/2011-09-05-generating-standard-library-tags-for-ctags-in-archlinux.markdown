---
author: admin
comments: true
date: 2011-09-05 06:21:53+00:00
layout: post
slug: generating-standard-library-tags-for-ctags-in-archlinux
title: archlinux使用ctags生成c/c++标准库tags
wordpress_id: 809
categories:
- Linux
tags:
- archlinux
- ctags
- vim
---

之前为了限制tags文件的体积，用脚本(修改后的脚本见本文最后)遍历的/usr/include/*.h和/usr/include下sys, bits,  asm, asm-generic,  netinet, arpa, c++4.6.1 以及 gtk-2.0， glib2.0几个目录。

但是当自动补全提示某些系统函数的原型时，比如write，无法正常找到，总是找到pth.h下的

    
    #define write pth_write


即使在生成的tags文件中确实有一句也不行：

    
    write	/usr/include/unistd.h	/^extern ssize_t write (int __fd, __const void *__buf, size_t __n) __wur;$/;"	p	signature:(int __fd, __const void *__buf, size_t __n)


诸位有知道答案的求赐教，先拜谢了。

被这恶心玩意逼疯了快，索性用pacman直接搜索glibc包含的标准库头文件，去掉一切用不到的东西。

    
    pacman -Ql glibc | awk '/include\//{print $2}'
    
    #for cpp， 后面的脚本中没有使用它
    pacman -Ql gcc | awk '/\/usr\/include/{print $2}'


这里是修改之后的脚本，原脚本是在chinaunix找到的，那个页面的地址找不到了<del>，作者名字也没记住</del>。 <del>如果你看到这个页面， 希望您告知下名字。</del>

UPDATE: **感谢原作者BSDC**

    
    #!/bin/bash
    #tags cscope生成文件路径
    sysTagsDir=~ggarlic
    tmpfilename="/tmp/asdfvisctagstmpfilename123.filesT%$"
    tmp4glibc="/tmp/qwertyuiop987.filesT%$" 
    
    rm -f $tmpfilename
    if [ $# -ne 1 ]; then
        echo "please specify a dir: sys or ."
        exit 1
    fi
    if [ "$1" = "." ]; then
        #只对当前目录下递归
        rm -f cscope.*
        rm -f tags
        find . -type f \
            -a \( -name "*.h" -o -name "*.cpp" -o -name "*.c" -o -name "*.cc" -o -name "*.java" -o -name "*.pc" \) > $tmpfilename
    elif [ "$1" = "sys" ]; then
        scanPath=(
        #基本没有使用这几个目录
        #而是使用pacman生成的列表来处理c标准库头文件
        #不然会找不到某些函数定义，而且生成的文件很大
        #"sys"
        #"bits"
        #"asm"
        #"asm-generic"
        #"netinet"
        #"arpa"
        #"glib-1.2"
        #"gtk-1.2"
        "glib-2.0"
        "gtk-2.0"
        "c++/4.6.1"
        #"boost"
        #"glibmm-2.4"
        #"gdkmm-2.4"
        #"gtkmm-2.4"
        #"opencv"
        #"opencv2"
        #"openssl"
        #"xorg"
        #"AL"
        #"GL"
        #"SDL"
        #"libxml2"
        )
    
        # get file list to be operating
        for dir in ${scanPath[*]}; do
            scanDir=$scanDir" /usr/include/"$dir
        done
    
        cd $sysTagsDir
        rm -f cscope.*
        rm -f tags
    
        pacman -Ql glibc | awk '/include\//{print $2}' >> $tmp4glibc
        #去掉生成列表里的文件夹行，只保留.h行
        grep h$ $tmp4glibc >> $tmpfilename
    
        find $scanDir -type f \
            -a \( -name "*.h" -o -name "*.cpp" -o -name "*.c" -o -name "*.cc" -o -name "*.java" -o -name "*.pc" \) >> $tmpfilename
    fi
    
    ctags -I "__THROW __nonnull __attribute_pure__ __attribute__ G_GNUC_PRINTF+ __wur" \
        --c++-kinds=+px --c-kinds=+px --fields=+iaS -a -f tags --extra=+fq --langmap=c:+.h --languages=c,c++ --c-kinds=+px --c++-kinds=+px -L $tmpfilename 
    
    #-k means kernel mode: don't parse /usr/include
    #-q: large project use this
    #cscope -Rqkb -i $tmpfilename
    cscope -Rb -i $tmpfilename
    rm -f $tmpfilename
    rm -f $tmp4glibc
    echo "Done!"

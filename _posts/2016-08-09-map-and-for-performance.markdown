---
author: ggarlic
comments: true
layout: post
slug: performance-difference-between-map-and-for
title: map 与 for 的性能对比
categories:
- Develop
---
预备知识：Python 对变量搜索是用的是环境模型，顺序是 Local - Enclosing - Global - Builtin，记不住的就记 LEGB (一句脏话)。简单点说就是在当前环境搜变量/方法，如果没有，就去上一环境搜，搜到 builtin 都没有，就会报错。

这段我主要对比 map 跟 for 循环。我们来看看 map 跟 for 的字节码都干了什么, 根据分析结果看看我们能不能做点什么优化。

先来看 map

```
In [14]: def map_loop():
   ....:     list(map(math.sin, range(100)))
   ....:    

In [15]: dis.dis(map_loop)
  2           0 LOAD_GLOBAL              0 (list)
              3 LOAD_GLOBAL              1 (map)
              6 LOAD_GLOBAL              2 (math)
              9 LOAD_ATTR                3 (sin)
             12 LOAD_GLOBAL              4 (range)
             15 LOAD_CONST               1 (100)
             18 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
             21 CALL_FUNCTION            2 (2 positional, 0 keyword pair)
             24 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
             27 POP_TOP
             28 LOAD_CONST               0 (None)
             31 RETURN_VALUE
```

载入全局 list 与 map 方法，载入 math 模块, 从中载入 sin 方法，载入全局 range 方法。
调用range方法，调用map方法，调用list方法。map是用c实现的。


再看for

```
In [12]: def for_loop():
   ....:     r = []
   ....:     for i in range(100):
   ....:         r.append(math.sin(i))
   ....:        

In [13]: dis.dis(for_loop)
  2           0 BUILD_LIST               0
              3 STORE_FAST               0 (r)

  3           6 SETUP_LOOP              42 (to 51)
              9 LOAD_GLOBAL              0 (range)
             12 LOAD_CONST               1 (100)
             15 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
             18 GET_ITER
        >>   19 FOR_ITER                28 (to 50)
             22 STORE_FAST               1 (i)

  4          25 LOAD_FAST                0 (r)
             28 LOAD_ATTR                1 (append)
             31 LOAD_GLOBAL              2 (math)
             34 LOAD_ATTR                3 (sin)
             37 LOAD_FAST                1 (i)
             40 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
             43 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
             46 POP_TOP
             47 JUMP_ABSOLUTE           19
        >>   50 POP_BLOCK
        >>   51 LOAD_CONST               0 (None)
             54 RETURN_VALUE
```

循环部分，19-47是主循环体，相当于是把 map 的循环变成 opcode 来做。但是我们一条一条看还有没有其他地方有问题。
22 保存 i 变量。25 拿到 r 变量。28 读 append 方法。31-34读取 math.sin 方法。37读取 i。40调用 math.sin 。43调用 r.append。

每次循环都会十分蛋疼的去 global 找 append 方法，去 global 找 math 模块。然后在 math 的方法字典里找 sin。这些东西完全可以放到 local 里，像 map 的参数一样只查找并载入一次

现在来看看修改版

```
In [16]: def local_var_for_loop():
   ....:     r = []
   ....:     local_append = r.append
   ....:     local_sin = math.sin
   ....:     for i in range(100):
   ....:         local_append(local_sin(i))
   ....:        

In [17]: dis.dis(local_var_for_loop)
  2           0 BUILD_LIST               0
              3 STORE_FAST               0 (r)

  3           6 LOAD_FAST                0 (r)
              9 LOAD_ATTR                0 (append)
             12 STORE_FAST               1 (local_append)

  4          15 LOAD_GLOBAL              1 (math)
             18 LOAD_ATTR                2 (sin)
             21 STORE_FAST               2 (local_sin)

  5          24 SETUP_LOOP              36 (to 63)
             27 LOAD_GLOBAL              3 (range)
             30 LOAD_CONST               1 (100)
             33 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
             36 GET_ITER
        >>   37 FOR_ITER                22 (to 62)
             40 STORE_FAST               3 (i)

  6          43 LOAD_FAST                1 (local_append)
             46 LOAD_FAST                2 (local_sin)
             49 LOAD_FAST                3 (i)
             52 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
             55 CALL_FUNCTION            1 (1 positional, 0 keyword pair)
             58 POP_TOP
             59 JUMP_ABSOLUTE           37
        >>   62 POP_BLOCK
        >>   63 LOAD_CONST               0 (None)
             66 RETURN_VALUE
```

37-59的循环体是不是干净了好多。顺便，我们来看看效果，代码稍微改了下,取10
次平均值



```
I: using for-loop
costs time 5.700465 s
I: using map()
costs time 3.968475 s
I: using local_var_for_loop
costs time 3.987862 s
```

是不是效果很明显。

代码在此: [https://gist.github.com/ggarlic/a6ba07a3c071a33915a6c638a29655a5](https://gist.github.com/ggarlic/a6ba07a3c071a33915a6c638a29655a5) 


总结下：map 是 c 实现的循环，循环中参数只载入一次。for 是 opcode 的，参数每次循环查找并载入一次。这差不多就是速度差异的原因了。列表解析你们可以试着dis一下，[官方的说法是大部分时候接近 map 甚至优于 map](https://wiki.python.org/moin/PythonSpeed/PerformanceTips#Loops), 至少在这个例子里 map 更快

PS: 代码里还有妄图手动减少循环次数的尝试，未果 -\_-! 

PS2: 本文是根据我在 xdlinux 邮件列表的[回答](https://groups.google.com/forum/#!topic/xidian_linux/sCMo15wAOLk)修改

PS3: 推荐阅读：上文提到的性能优化的文档 [https://wiki.python.org/moin/PythonSpeed/PerformanceTips](https://wiki.python.org/moin/PythonSpeed/PerformanceTips)

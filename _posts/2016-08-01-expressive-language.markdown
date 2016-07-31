---
author: ggarlic
comments: true
layout: post
slug: expressive-language
title: Expressive Language
categories:
- Develop
---

最近第一次领会了什么叫富于_表现力_的语言。

最近在看 [EOPL](https://book.douban.com/subject/1761927/), 做到1.17的时候发现让实现个排序。
很自然的，想到了 Haskell 官网上那段著名的[伪快排](https://wiki.haskell.org/Introduction#Quicksort\_in\_Haskell)\*:

```haskell
qsort []     = []
qsort (x:xs) = qsort (filter (< x) xs) ++ [x] ++ qsort (filter (>= x) xs)
```

照葫芦画瓢来段 Scheme 版:

```scheme
(define sort
  (lambda (lon)
    (if (null? lon)
        '()
        (append (sort (filter-in
                       (lambda (x)
                          (< x (car lon)))
                       (cdr lon)))
                (list (car lon))
                (sort (filter-in
                       (lambda (x)
                          (>= x (car lon)))
                        (cdr lon)))))))
```

这表现力差得大了点吧, -\_-!

\*: 为什么说是\*伪\*呢, 快排要求原地。参考[这里](http://stackoverflow.com/questions/7717691/why-is-the-minimalist-example-haskell-quicksort-not-a-true-quicksort)

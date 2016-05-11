---
title: TooEasy Or TooDifficult
date: 2016-05-11 3:49:00
tags: manacher 异或
---

### J. TooEasy Or TooDifficult

#### 题意

- 给你定义两类值，P[i]表示以第i个字符为中心的最长回文串长度，MZ和max{P[i]}有关，FJD为连续若干个P[i]异或和的最大值。问你MZ和FJD谁大

#### 分析
1. 解决MZ就是最裸的manacher算法，FJD则利用到一个性质sum[j]^sum[i] = p[i]^...P[j];这样转换之后就是求最大的sum[j]^sum[i],这个问题在问题A中谈论过了，就是建立01二叉树，然后把sum[i]全部插入，再全部走一遍

2. 当时生成插入01树的时候写挫了，一直死循环，debug半天

#### 思考
1. 没啥坑点

2. 常规题
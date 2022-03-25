---
mathjax: true
title: 2015 年 ACM/ICPC 合肥现场赛
tags:
  - Trainings
  - Suffix Array
categories:
  - 训练
  - 2019 年暑假
date: 2019-07-04 15:34:55
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  15  |   6    |  Ø  |  .  |  .  |  Ø  |  O  |  .  |  O  |  O  |  O  |  .  |

<!--more-->

# A

求 $n$ 个点含有环的 $m$ 染色联通图个数。

见 [题解](https://blog.csdn.net/Umbrella__/article/details/80754861)。

# D

见 [HDu5555 Immortality of Frog 题解](https://xlor.cn/2019/7/hdu5555/)。

# E

Solved by XLor.

枚举古代遗迹，二分图匹配。

# G

Solved by Henry and XLor.

预处理后缀排序后的下标最小值 ST 表。

每次询问 $i$ 时，对于他的排序后左右区间，二分一个最近的下标小于其的位置，然后暴力调整这个位置的原下标最小值而不改变 LCP 长度。

# H

Solved by Henry and Forsaken.

构造。

# I

Solved by Forsaken.

cdq 分治。
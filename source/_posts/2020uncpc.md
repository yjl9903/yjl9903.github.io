---
mathjax: true
title: 2020 ICPC Universidad Nacional de Colombia Programming Contest
tags:
  - Trainings
categories:
  - 训练
date: 2020-09-22 19:12:36
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  |  N  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  13  |   11   |  Ø  |  O  |  O  |  O  |  O  |  O  |  O  |  O  |  O  |  .  |  O  |  O  |  O  |  O  |

<!--more-->

# A

UpSolved by XLor (-9).

注意到所求的是一个二次函数，直接算精度不够，改成三分即可。

# B

Solved by XLor (01:29, +1).

显然，第二个串取一个字典序最大的后缀，第一个串也从字典序最大的后缀起，取到第一个比第二个串首字母小的位置。

内鬼忘记 `break` 了。

# C

Solved by XLor (02:29, +1).

令 $f(n)$ 表示长度为 $n$ 的本原串个数，枚举因子容斥一下即可。

# D

Solved by XLor (01:17).

矩阵快速幂。

# E

Solved by miaojie (01:00).

交互。

# F

Solved by XLor (03:37, +1).

分层图最短路。

# G

Solved by miaojie (00:28).

# H

Solved by miaojie (04:23, +1).

求奇数长度的本质不同回文串。

# I

Solved by XLor (02:08).

预处理每个点向右向左，第一个大于等于它的，然后转化为连续相同的取最后一个，这样一定不劣。

显然，该图是一个 DAG，求 DAG 最长路即可。

# K

Solved by XLor (00:16, +1).

# L

Solved by XLor (03:13, +2).

求字典序最小的路径。

# M

Solved by XLor (00:24).

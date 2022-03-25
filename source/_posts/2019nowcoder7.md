---
mathjax: true
title: 2019牛客暑期多校训练营第 7 场
tags:
  - Trainings
  - Data Structure
  - DP
categories:
  - 训练
  - 牛客多校
date: 2019-08-08 19:59:33
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  5   |   10   |  O  |  O  |  O  |  O  |  O  |  Ø  |  .  |  O  |  O  |  O  |  O  |

<!--more-->

# A

Solved by XLor.

预处理每个区间是否是 Lydon word，然后 dp 一下，倒着恢复出方案。

# B

Solved by Forsaken.

签到。

# C

Solved by XLor.

枚举每种树作为最大高度，在前缀中查询费用和前 $k$ 小之和。

# D

Solved by Henry.

签到。

# E

Solved by Henry, Forsaken and XLor.

动态开点线段树，ODT，正解。

离散化后，每个点维护一个左闭右开的区间。

# F

UpSolved by XLor.

有点意思。

把答案转化为算每个位置的贡献，扫描线维护出哪些时刻消除了当前枚举的位置。

最终答案，特判第一个时刻的影响，需要求每个分割出的时间段，有多少多时长大于该点到达最大值所需的时间，和没有到达最大值的总时间和。

树状数组上维护一下每种时间段的前缀和。

# G

UnSolved by XLor.

$O(n\sqrt{n}\log n)$ 的辣鸡做法：王室联邦分块，每个块存个 `std::set` 维护内部点的下标，块内快外分别暴力即可。

更新：↑ 假的。

# H

Solved by Henry.

好多维的数位 $dp$。

# I

Solved by Henry.

# J

Solved by XLor.

签到。

# K

Solved by Forsaken.

2018年 ICPC 北京赛区原题。
---
mathjax: true
title: 2019 CCPC 哈尔滨现场赛
tags:
  - Trainings
categories:
  - 训练
  - 现场赛
date: 2019-10-23 20:35:03
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  9   |   7    |  !  |  O  |  !  |  .  |  O  |  O  |  .  |  .  |  O  |  O  |  O  |  O  |

<!--more-->

# A

UnSolved by Forsaken.

二分答案，差分约束系统建图。

正解：最小平均环。

# B

Solved by Henry.

# C

UnSolved by All.

小模拟。

# E

Solved by Henry and XLor.

只需要求出最后生成序列的大于一半众数和总个数。

倒着扫一遍，记录每个点对总答案的贡献系数，即拓扑序。

# F

Solved by Henry.

# I

Solved by Forsaken.

# J

Solved by Henry.

# K

Solved by Henry.

# L

Solved by XLor.

枚举每个前缀，搞出所有有效串，丢进去哈希一下。

询问时，枚举每个前缀的有效串，看是否是其后缀，注意特判有 $0$ 的情况和全 $0$ 的情况。

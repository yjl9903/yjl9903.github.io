---
mathjax: true
title: 2020 牛客暑期多校训练营第 6 场
tags:
  - Trainings
  - Combinatorial
  - DP
categories:
  - 训练
  - 牛客多校
date: 2020-07-27 20:46:13
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  20  |   8    |  O  |  O  |  O  |  .  |  O  |  .  |  O  |  O  |  .  |  O  |  O  |

<!--more-->

# A

Solved by ytriayggg and XLor (04:41:31).

首先，注意到答案就是将排列转换成置换群，每个环进行操作。注意到，我们应该尽量选大的环，这样有更多机会得到正确的位置。然后，对于一个大小为 $n$ 的环，我们使用一次混洗操作，会将其分割成多个环。利用这个思路算一算贡献。

# B

Solved by ytriayggg (01:37:16).

结论，答案是 $\prod_{i=0}^{n-1}\frac{2^n-2^i}{2^n}$。

# C

Solved by XLor (00:54:49, +1).

所有数都是正的，如果我们选择多列，那么答案一定不会更优。

# E

Solved by miaojie (00:27:17, +2).

通过 $\sum_{i=1}^n i$ 判断后，使用相邻两个和为 $n$ 进行构造。

# G

Solved by miaojie (03:10:47, +4).

喵喵构造。

# H

Solved by ytriayggg (03:10:47, +2).

数位 DP。

# J

Solved by XLor (04:49:29, +3).

对于每次询问，使用树状数组维护第 $k$ 大求出置换，快速幂即可。

# K

Solved by XLor (03:33:28, +4).

枚举第一段的长度，后序预处理（哈希）判断是否为 $1$ 到 $k$ 的排列。

背锅：$k \ge n$ 时，可能存在两段，不一定必须满足所有数不同。

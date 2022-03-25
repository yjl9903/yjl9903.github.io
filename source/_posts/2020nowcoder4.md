---
mathjax: true
title: 2020 牛客暑期多校训练营第 4 场
tags:
  - Trainings
categories:
  - 训练
  - 牛客多校
date: 2020-07-21 01:21:39
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  91  |    7   |  Ø  |  O  |  Ø  |  Ø  |  .  |  O  |  .  |  O  |  Ø  |  .  |

<!--more-->

# A

UpSolved by XLor (+2).

首先，考虑单组询问的做法，二分答案 $mid$，我们首先选一个最深的点，往上走 $mid$ 步，那么这整棵子树都满足了条件，可以删除，重复这个过程直到取到根节点。

进一步观察，对于某个最大长度 $k$，上述迭代至多进行 $O(n / k)$ 次，因为最坏也是删除一个条长度为 $k$ 的链。

因此，我们对于答案等于 $k=1,2,\dots,n$ 时，分别计算出需要多少个关键点，即可推出每个关键点个数对应的答案。这个过程由于调和级数，总数是 $O(n\log n)$ 的，单次操作需要花费 $O(\log n)$ 查询数据结构，总体时间复杂度是 $O(n\log^2n)$。

# B

Solved by ytriayggg (00:09:18).

签到。

# C

UpSolved by ytriayggg (+2).

# D

UpSolved by XLor (+9).

首先，注意到一个结论答案至多 $9$，因此每个数字的位数差至多为 $1$。

因此，分为两种情况，所有数字位数都相同，枚举位数，模拟大数计算答案，时间复杂度 $O(n\sqrt{n})$。

第二种情况，位数差为 $1$，那么必然是 $1000\dots 000 \text{?} $ 或者 $9999\dots 999 \text{?}$ 这两种情况。

# F

Solved by miaojie (00:15:10).

# H

Solved by miaojie and ytriayggg (01:04:34, +1).

# I

Solved by miaojie (+17).

乱搞。

---
mathjax: true
title: 2020 牛客暑期多校训练营第 8 场
tags:
  - Trainings
  - String
categories:
  - 训练
  - 牛客多校
date: 2020-08-03 22:52:43
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| 129  |   3    |  Ø  |  .  |  .  |  .  |  .  |  .  |  O  |  Ø  |  O  |  .  |  O  |

<!--more-->

# A

UpSolved by XLor (+3).

答案就是联通块个数减去孤立的篮球队数。

# E

UnSolved by ytriayggg.

# G

Solved by XLor (01:25:28, +1).

$O(C(256,3))$ 冲冲冲。

# H

UpSolved by XLor.

预处理最小循环节和最小循环同构并去重后。

有引理，两个串的无穷串，要么他们本质相同，要么长度不超过长串的 $3$ 倍。

考虑将最短串和其他所有串求 LCS，那么其他串需要倍长 $6$ 倍，最短串倍长到最长串的 $6$ 倍，跑一下即可。

# I

Solved by XLor (01:38:07, +2).

HK 水过去了。

正解是判断每个点连通块是否有环，有环则能全拿，否则会留下一个。

# K

Solved by ytriayggg (02:37:41, +6).

贪心选前缀。

注意答案为 $10^{19}$ 级别。

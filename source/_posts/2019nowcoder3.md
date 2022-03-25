---
mathjax: true
title: 2019 牛客暑期多校训练营第 3 场
tags:
  - Trainings
  - Data Structure
  - Sqrt
  - DP
  - Divide and Conquer
categories:
  - 训练
  - 牛客多校
date: 2019-07-26 16:17:44
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  38  |   7    |  Ø  |  O  |  .  |  O  |  .  |  Ø  |  O  |  O  |  .  |  O  |

<!--more-->

# A

UpSolved by XLor.

对度数分块，对边序列分块。

边序列的块上打标记，暴力修改时维护对大点的贡献。

小点直接枚举每条边，大点枚举每个块对他的贡献。

大点的具体实现方式，对大点重新标号，有 $\sqrt{n}$ 个大点，每个块上记录重新标号后的大点贡献，维护出块上整体翻转标记为 $0/1$ 时的两种贡献。

# B

Solved by XLor.

子序列贪心，子串将 $0/1$ 转化为 $+1/-1$ 后做前缀和，维护出每种值的第一个和最后一个出现位置。

# D

Solved by Forsaken.

exBSGS 求出离散对数的循环节，枚举 $i^j$ 上的 $j$ 算贡献，$j$ 只需要计算小于等于 $30$ 的情况，大于 $30$ 的和 $30$ 一致。

# F

UpSolved by Henry and XLor.

枚举上下边界，搞出每列的最大值 / 最小值，枚举右边界，用两个单调队列维护出左边界。

# G

Solved by Forsaken and Henry.

原题等价于计数 $(l,r)$，满足 $\max_{i=l}^r a_i <= 2 \sum_{i=1}^r a_i$。

分治，复杂度 $O(n \log^2 n)$。

# H

Solved by Henry.

排序后找到中间点，稍微偏一下即可。

# J

Solved by XLor.

模拟一下双向链表即可。
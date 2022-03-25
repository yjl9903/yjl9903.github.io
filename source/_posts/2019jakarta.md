---
mathjax: true
title: 2019 Asia Jakarta 区域赛
tags:
  - Trainings
categories:
  - 训练
date: 2019-11-14 17:46:40
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  32  |   11   |  O  |  O  |  O  |  Ø  |  O  |  O  |  O  |  O  |  .  |  O  |  O  |  O  |

<!--more-->

# A

Solved by Henry.

# B

Solved by Henry and XLor.

树形 dp。

每个结点 $u$ 分成 $3$ 个状态，$u$ 被一条拐弯的路径覆盖，$u$ 往上延伸，分成是否允许中断两种。

注意最后两个状态的区别，考虑一种情况， $u$ 是一个路径的最浅端点，儿子 $v$ 也是他的路径最浅端点，这样两条路径需要合并。

转移时，无法进行除法，预处理前后缀和即可。

# C

Solved by Henry.

# D

UpSolved by XLor.

枚举每个询问的转折点，拆成 $\sum |s|$ 个询问，每个询问点对应正向 Trie 树和反向 Trie 树的一个点对。

从左到右，预处理每个横行的正向 Trie 图状态；从下往上，预处理每个竖列的反向 Trie 图状态。因此，每个点作为一个交点的状态也是一个点点对。

对每个询问，就是在正向的 fail 树上，询问子树内有多少点，在反向 fail 树上也在其子树内。离线所有询问，二维数点即可。

# E

Solved by Henry.

# F

Solved by Forsaken and Henry.

# G

Solved by Forsaken and Henry.

# H

Solved by Forsaken.

# J

Solved by Forsaken.

dp 一下前 $i$ 个有 $j$ 个石头，最多能用多少个 $1 \times 2$ 的石头。

枚举一下用了多少个石头，讨论一下，可以 $O(1)$ 算出最小花费。

# K

Solved by Forsaken, Henry and XLor.

转移是一个矩阵，更新是矩阵的两个对角线分别交换。

# L

Solved by Henry and XLor.

所有环边连向一个虚拟的油漆，跑一下最大匹配。

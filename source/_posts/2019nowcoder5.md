---
mathjax: true
title: 2019牛客暑期多校训练营第 5 场
tags:
  - Trainings
  - DP
  - Math
  - Graph
categories:
  - 训练
  - 牛客多校
date: 2019-08-02 18:49:32
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  34  |    8   |  O  |  O  |  Ø  |  .  |  Ø  |  Ø  |  O  |  O  |  O  |  .  |

<!--more-->

# A

Solved by Henry.

签到。

# B

Solved by Henry.

十进制下的快速幂。

# C

UpSolved by Forsaken.

无视取模，递推式是一个类等比数列。

讨论一下：

1. $a=0$ 时，数列为 $x_0, b, b, \dots$，直接回答即可。

2. $a=1$ 时，数列为等差数列 $x_0, x_0+b, x_0+2b, \dots$，即求解 $v \equiv x_0+ xb (\bmod p)$，$x\equiv b^{-1}(v-x_0)(\bmod p)$，预处理逆元。

3. 其他情况，令 $y_i=x_i+{b \over a-1}$，因此由原式 $y_i=ay_{i-1}$，即求解 $v+{b \over a-1} \equiv a^x(x_0+{b \over a-1})(\bmod p)$，等价于求解 $a^x \equiv (v+{b \over a-1})(x_0+{b \over a-1})^{-1}(\bmod p) $，BSGS 求解。

注意，情况 $3$ 的 BSGS 需要需处理一些东西，参见题解。

# E

UpSolved by XLor.

预处理邻接关系的二进制表示。

考虑状压 DP，枚举每个状态，转移时，只需要枚举某一个点是否选上即可。

因为，若我们取定点击中的某个点，考虑这个点集的最大独立集，只有两种情况，包含和不包含这个点，因此转移是 $O(1)$ 的。

# F

UpSolved by XLor.

建反图，发现是二分图求最大独立集，贴一个 HK 算法和输出方案，参见模板库。

# G

Solved by Henry.

DP。

# H

Solved by Henry.

如果答案存在，答案唯一。

归并排序 / 拓扑排序。

# I

Solved by Henry.

固定宽度，求放下一个三角形的最大高度，注意精度。
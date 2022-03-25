---
mathjax: true
title: 2016年 ACM/ICPC 沈阳现场赛
tags:
  - Trainings
  - Math
  - Graph
categories:
  - 训练
  - 2019 年暑假
date: 2019-07-07 17:55:49
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  64  |    6   |  O  |  O  |  O  |  .  |  O  |  .  |  .  |  Ø  |  .  |  !  |  Ø  |

<!--more-->

# A

Solved by Forsaken.

# B

Solved by Forsaken.

# C

Solved by Forsaken.

# E

Solved by XLor.

从标号小的连到标号大的，dfs 暴搜即可。

# H

UpSolved by XLor.

建出 AC 自动机上的 dp 转移矩阵 $A$，初始矩阵为 $X$，稳定状态下的概率矩阵为 $B$。

由 $B=A^{\infty} \times X$，得到 $(A-I)\times B = X$，高斯消元解出这个关于 B 的线性方程即可。

# J

UnSolved by XLor.

一个点的儿子 bfs 序是连续的，讨论三种情况，线段树更新即可。

# K

UpSolved by Forsaken.

原根 + FFT。
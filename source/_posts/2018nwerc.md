---
mathjax: true
title: 2018-2019 ICPC Northwestern European 区域赛
tags:
  - Trainings
  - Graph
  - Compiler
  - Math
categories:
  - 训练
date: 2020-09-29 01:30:41
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  115 |   6    |  Ø  |  O  |  Ø  |  Ø  |  O  |  .  |  O  |  O  |  O  |  Ø  |  O  |

<!--more-->

# A

UpSolved by XLor.

注意到两维独立，问题转化为给定 $y_1, y_2, \dots, y_n$，构造 $x_1 \le x_2 \le \dots \le x_n$，最小化 $\sum_{i=1}^n (x_i-y_i)^2$。

函数 $f(x)=\sum_{i=1}^n (x-a_i)^2$ 的最小值，在 $x=\frac{\sum_{i=1}^n a_i}{n}$ 处取到。

我们没加入一个新的目标点 $a_i$，显然其最值在 $a_i$ 处取到，若其在前一次的点的右侧，那么没有问题。否则，应该将两个二次函数的最值点合并，即合并二次函数个数和 $a_i$ 之和。于是，我们得到了一系列点，根据此计算出最小值。

# B

Solved by miaojie (01:31).

# C

UpSolved by miaojie.

将角度分成 $n-1$ 份即可，按照类似于 $(dep(i), dfn(i))$ 的方式构造。

赛中的方案，如果非二度结点的层数过多，导致分母过大，两个点重合。

# D

UpSolved by XLor.

给定一个有向带权图，一个人从起点开始等待一段时间后出发，在图上随意游走，当时间处于 $[A,B]$ 中的任意一个时刻，终点发出信号，他会立马（若他正在边上，则会在到达后出发）沿着最短路走到终点，求这个最短路的最大值。

显然，答案必定不大于最短路，因为可以一直在起点等待，发出信号才开始出发。

二分答案，令终点等待的时间为 $\delta < d(S,T)$。

我们考虑终点前的一个点 $x$，若 $d(S,x)+d(x,T)>A+\delta$，那么走到 $x$ 点必然无用，其实从一开始就走，在 $d(S,x)$ 收到信号，他也不能在 $A+\delta$ 时刻到达。因此，我们令满足 $d(S,x)+d(x,T) \le A+\delta$ 的点 $x$ 为好点。然后，若我们从好点出发走一条长度为 $l$ 的边，走到 $v$ 点，满足 $l+d(v,T)$ 不超过 $\delta$，也就是我们可以从好点出发走一条边来消磨时间。

根据上述过程，扣出一个子图，若子图中有环，那么可以一直在环上消磨时间，直到信号发出。

或者图是一个 DAG，我们希望尽量在起点多等一会，使得在 $[A,B]$ 这个时间段内，都能响应信号的发出。

一开始，每个点的最晚到达时间是 $A+\delta-dis(x,T)$，然后考虑在边上走消磨得时间，也就是在拓扑序上 DP 出每个点的最晚到达时间，若其不小于 $B$，则合法。

# E

Solved by XLor (02:13, +4).

注意到，要么 `Shuffle` 单一元素，要么必须 `Shuffle` 的方式完全相同，才可能等价。

使用递归下降分析器，构造出语法树，然后将语法树等价于一堆的 `List`，`Sorted` 和 `Shuffle` 拼接在一起，将序列打平后，判断是否完全相同。

# G

Solved by miaojie (04:24, +6).

从原点开始，螺旋构造，注意避免不同层之前产生冲突。

# H

Solved by miaojie (00:27).

# I

Solved by miaojie (00:11).

# J

UpSolved by XLor.

题意是，给定一个数 $x$ 和一个长度为 $n-1$ 的数组，数组所有数不大于 $x$，每次选择数组中最大数的一半（上取整），其他位置均 $+1$，求多长时间后最大值大于 $x$。

观察这个过程，可以发现如果当前最大值有 $y$ 个，那么连续操作 $\log_2(k)$ 次，每次最大值 $+1$，此时只剩一个最大值，再操作一次，最大值匀平，且次大值和最大值的差距缩小 $1$。

因此，维护最大值的合并过程即可。

# K

Solved by XLor (00:25).

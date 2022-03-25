---
mathjax: true
title: 2014 年 ACM/ICPC 北京现场赛
tags:
  - Trainings
  - DP
categories:
  - 训练
  - 2019 年暑假
date: 2019-06-29 19:21:38
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  3   |   10   |  O  |  O  |  O  |  O  |  Ø  |  O  |  !  |  O  |  O  |  Ø  |  O  |

<!--more-->

# A

Solved by Henry.

签到。

# B

Solved by Henry(-3).

直接搜。

将一定不存在的解判掉，即存在一个颜色特别多。

# C

Solved by Henry(-2).

同余方程。

# D

Solved by Henry and Forsaken(-1).

区间 DP，令 $dp[l][r]$ 表示这个区间的答案。

初始化不合法区间为 $0$，其余为无穷大。

从后往前想，转移时，枚举中间点，先将两边全部拿走，最后拿走中间点，拿走中间点的代价是 $b[l-1]+b[r+1]$。

答案是 $a$ 序列的和加上 $dp[1][n]$。

# E

UpSolved by Forsaken(-7).



# F

Solved by XLor.

[Algorithms Lecture #2 - EV and Sums of Small Powers](https://www.bilibili.com/video/av42511111/)。

$E(X^3)$ 改成枚举三元组，即枚举三个灯，令 $dp[i][j][k]$ 为 $3$ 个灯的开关状态，简单概率 dp 转移，答案加上 $dp[1][1][1]$。

# G

UnSolved by XLor(-4).

预处理小于等于 $10^8$ 的答案，T 了。

实际上很容易 hack，构造一条长链，相邻两点之间有两条边，每个点都是关键点，然后串的个数飞速增长就凉了。

# H

Solved by Forsaken(-1).

线性基。

# I

Solved by Henry.

两个圆环面积交，容斥。

# J

UpSolved by Henry and XLor.

# K

Solved by XLor.

签到，贪心。
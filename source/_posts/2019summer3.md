---
mathjax: true
title: 2016 年 CCPC Final
tags:
  - Trainings
  - Graph
categories:
  - 训练
  - 2019 年暑假
date: 2019-07-01 16:41:59
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  30  |   9    |  O  |  O  |  .  |  .  |  Ø  |  Ø  |  O  |  O  |  O  |  O  |  .  |  O  |

<!--more-->

# A

Solved by Henry.

# B

Solved by Henry and XLor.

洗衣机贪心。

洗衣和晒干的过程独立。

一个过程就用优先队列来贪心。

# G

Solved by XLor.

最小环。

枚举删边，跑最短路，注意细节优化即可。

# E

UpSolved by XLor.

妙啊！

赛中冲的算法是线段覆盖，对目标题目询问未被覆盖次数，取 $\min$ 后加一。

显然非常的假，你不能保证剩余的线段一定能覆盖所有其他的点。

将线段端点和询问点，拆成事件做扫描线，对于相同端点的事件，先加入，再询问，最后删除。

对于询问，看能不能覆盖这个点，即有当前存在事件集合非空，答案是线段数减去集合大小，并要拿去一个线段，因为这个线段不能对其他询问产生贡献。这里需要贪心的想，右端点应该尽量靠前，这样才会减少对后面询问的影响。

# H

Solved by Forsaken.

状压 $dp$ + 背包。

预处理每个人的可行状态，然后用 $dp[i][mask]$，表示用了前 $i$ 个状态为 mask 的个数，用记录的状态进行背包转移。

# I

Solved by Henry.

预处理所有物品的价值 + 完全背包。

# J

Solved by Henry.

# L

Solved by Henry.

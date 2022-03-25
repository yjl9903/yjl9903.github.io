---
mathjax: true
title: 2018 ACM-ICPC Southern Subregional Contest 训练
date: 2018-10-21 00:42:57
tags:
- Trainings
categories:
- 训练
---

传送门：http://codeforces.com/contest/1070

| solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  | 
| :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|    6   |  !  |  .  |  O  |  O  |  Ø  |  O  |  .  |  O  |  .  |  .  |  O  |  .  |  .  |

<!--more-->

# A

Unsolved.

wb 尝试推一波结论，失败。

# C

Solved by XLor.

对于每个计划按照价格升序，数量降序排序。

按顺序更新一棵线段树，维护区间剩余CPU的最大值和最小值，进行剪枝和更新。

答案无需维护，直接使用全局变量更新即可。

# D

Solved by wb.

# E

Unsolved.

明显的二分题。

然而二分的check条件没写好。

赛后参考 FazzyDavid 通过。

# F

Solved by wb.

# H

Solved by wb && XLor.

$10000$ 个串，每个串长度小于等于 $8$，询问 $q$ 次一个串是前面多少串的子串。

直接预处理每个串的所有子串的 Hash 值。

注意：Hash时每个字符应该大于 0，否则 Hash(b) = Hash(aab)。

# K

Solved by XLor.

数组开小了，贡献一波罚时。
---
mathjax: true
title: 2020 Moscow Workshops Programming Camp Day 3
tags:
  - Trainings
categories:
  - 训练
  - Moscow Workshops Programming Camp
date: 2020-05-04 01:13:17
password: bytecamp_day3_xysj's_team
abstract: 不给你看.
message: 想想办法?
wrong_pass_message: 抱歉, 您的办法不太行, 请再想想?
wrong_hash_message: 抱歉, 这篇文章不能被校验, 不过您还是能看看解密后的内容?
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  44  |   8    |  O  |  Ø  |  O  |  .  |  .  |  .  |  O  |  O  |  O  |  O  |  Ø  |  .  |

<!--more-->

# A Farmer

Solved by ytriayggg (0:50:12).

# B MST Camera

UpSolved by XLor.

使用二维 $ST$ 表维护一个块 $MST$，两个块归并 $MST$。

询问矩形最多被拆成 $6$ 个正方形。

# C Winning Ballot

Solved by miaojie (0:24:10).

# G Rats

Solved by XLor (1:47:33, +3).

将 $a$ 串替换为其最短循环节。

如果输入串 $s$ 是 $a_i^{\infty}$ （$a_i$ 表示 $a$ 的循环移位）的前缀，那么就连一条 $i$ 指向 $(i + |s|) \bmod |a|$ 的边。

答案就是图的最小环。

# H Coins

Solved by ytriayggg (0:15:28).

问题的两维独立。

# I Marbles

Solved by XLor (4:27:25, +27).

假板害人，HK 算法边没有清空。

首先考虑如何判断一个时间是否合法，每个球可以选左右走到每个洞内，将图建出来，判断是否存在完美匹配。

显然，时间有循环节 $2L$，只需要考虑小于 $2L$ 的时间；显然不能枚举时间，但是第一个球一定要进洞，那么枚举它的进洞时间，判断剩下的是否合法。

注意到，这个图每个左部的度数至多为二，因此该图存在完美匹配当且仅当它只含有奇数长度的链和偶环。

# J Notebook

Solved by ytriayggg (2:15:48, +5).

线段树多项式 $\gcd$。

# K Aunts

UpSolved by XLor.

二分答案。

盘面合法当且仅当：

1. $x_i+y_i+h_i$ 奇偶性相同；

2. 对任意 $i, j$ 满足 $|h_i-h_j| \le |x_i+x_j|+|y_i+y_j|$。

使用分治判断第二个条件。

第一维是 $x$，第二维是 $y$ 归并后维护最值来判断。

注意：归并要左大右小，左小右大，归并两次。

<script>
  MathJax.Hub.Queue([Typeset, MathJax.Hub]);
</script>
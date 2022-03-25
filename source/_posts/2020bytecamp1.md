---
mathjax: true
title: 2020 Moscow Workshops Programming Camp Day 1
date: 2020-05-02 00:19:36
tags:
  - Trainings
categories:
  - 训练
  - Moscow Workshops Programming Camp
password: bytecamp_day1_xysj's_team
abstract: 不给你看.
message: 想想办法?
wrong_pass_message: 抱歉, 您的办法不太行, 请再想想?
wrong_hash_message: 抱歉, 这篇文章不能被校验, 不过您还是能看看解密后的内容?
---

传送门：[ByteDance - Moscow Workshops Programming Camp 2020. Upsolving.](http://opentrains.mipt.ru/~ejudge/team.cgi?contest_id=002696)

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  48  |   9    |  O  |  O  |  O  |  Ø  |  Ø  |  .  |  .  |  O  |  O  |  Ø  |  Ø  |  .  |

<!--more-->

# A Allowed Swaps

Solved by miaojie and ytriayggg (2:37:28).

将图建出来，对于一个联通块显然要位置和里面值一一对应。

对于一个联通块，若它是一棵树，那么可以从叶子往上构造。但是现在是一个一般图，限制更弱，扣出生成树后，做类似的过程。

# B Banned Words

Solved by XLor (0:21:27).

给你总长不超过的 $100$ 的字符串集合，数一下长度为 $L$ 的串不包含集合内任何一个串。

# C Cost of Subtree

Solved by XLor (0:10:41).

定义一个联通子图的权值是边权最小值和边数的乘积，求权值的最大值。

将边拿出来从大到小排序，一个个加入图中，每加入一条边，更新联通块的最大权值。

# D Disk Troubles

UpSolved by ytriayggg.

BSGS.

# E Embeddings

UpSolved by XLor.

回忆回文树的一个结论：每个回文串的回文子串，要么是两头缩一段，要么是最长回文后缀（前缀）的子串。

现在问题就变成，不重不漏的遍历这棵回文树，来维护 $dp$ 值。

记 $p(s)$ 表示 $s$ 的左右缩一个的串，$b(s)$ 表示 $s$ 的最长回文后缀。

令 $f(u)$ 表示选 $u$ 这个串的方案数，$g(u)$ 表示选或不选的方案数。

因此有转移方程：

$$
f(u)=1+g(p(u)) \\
g(u)=f(u)+g(p(u)) + 2 \sum_{v \in border(u)} f(v)
$$

上面的转移还需要维护 `border` 上的前缀和，最后计算答案就是每个右端点的这个值之和。

# H Histogram and Blue Rectangles

Solved by XLor (2:31:16, +1).

求一个直方图的每个前缀的最大子矩阵。

考虑维护一个单调递增栈，新加入的矩形高度过小，会将某一个后缀的高度削成和它一样。

因此，每个时刻得到了一个阶梯状的图形，显然只有每个阶梯的左端点有用，当前的答案就需要枚举当前位置的左端点。

注意到需要优化的式子是一个斜率的形式，又考虑到有插入和删除，离线后线段树分治。

参考：[BZOJ4311 向量](https://www.cnblogs.com/clrs97/p/4919231.html)。

> XLor: 不会李超树，硬改李超树浪费时间太多。

# I Integer Equation Checker

Solved by ytriayggg (2:12:15, +2).

大模拟。

# J Joy With Cookies

UpSolved by XLor.

给定 $n$ 种长 $x_i$ 宽 $y_i$ 的矩形，有 $k$ 个矩形为地基，你需要为这 $k$ 个地基安排是否旋转 $90$ 度。

两人玩一个游戏，使用这 $n$ 种矩形，不断往这 $k$ 个上面放，放的时候必须满足比下面的长宽严格小，且你无法旋转，无法操作的人失败。

求是否存在分配方案，使得先手必胜，若先手不是必胜，你需要构造一个方案。

题目套了两层博弈，内层博弈是一个 $SG$ 游戏，考虑计算后继状态的 $mex$，但是由于偏序关系的传递性，$mex$ 与 $\max$ 等价。

因此，你可以使用扫描线的方式计算出每个矩形的 $SG$ 值。

外层博弈是一个 $nim$ 游戏，套用 $nim$ 的结论，使用线性基（高斯消元）进行检查。

# K Knights of Round Table

UpSolved by Solved by miaojie and ytriayggg.

<script>
  MathJax.Hub.Queue([Typeset, MathJax.Hub]);
</script>

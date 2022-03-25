---
mathjax: true
title: 2020 Moscow Workshops Programming Camp Day 2
tags:
  - Trainings
categories:
  - 训练
  - Moscow Workshops Programming Camp
date: 2020-05-03 01:38:32
password: bytecamp_day2_xysj's_team
abstract: 不给你看.
message: 想想办法?
wrong_pass_message: 抱歉, 您的办法不太行, 请再想想?
wrong_hash_message: 抱歉, 这篇文章不能被校验, 不过您还是能看看解密后的内容?
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  51  |    7   |  O  |  Ø  |  Ø  |  .  |  O  |  .  |  .  |  O  |  .  |  O  |  .  |  !  |  Ø  |

<!--more-->

# A Allocation

Solved by XLor (1:34:27, +5).

模拟，题意有毒。

# B Binary Trees

UpSolved by XLor.

注意到一个结论：一棵树是 BST，当且即当中序遍历序列递增。

将中序遍历序列按照递增关系分割开来，修改时动态维护答案的增量。

问题就变成计算一个区间内包含多少个子树，离线后二维数点即可。

# C Counting Bit Strings

UnSolved by ytriayggg.

一个暴力的做法就是枚举选定前 $i$ 个，维护每个长度为 $L$ 的后缀的 $1$ 的状态，进行转移。

# E Edge Sets

Solved by XLor (0:25:58, +1).

虚树的模板题。

# H Hoodies

Solved by ytriayggg (0:37:35).

树状数组。

# J Jury Troubles

Solved by miaojie (3:00:09, +1).

原图是一个二分图，对矛盾关系建二分图，求最大独立集。

# L Longest Arc

UnSolved by XLor.

题解：将圆的切点抠出来跑凸包，然后枚举一下弧。

# M Minimize the Similarity

UpSolved by XLor.

为输入的字符串集合指定一个顺序，最小化相邻两个的 `LCP` 之和，给出一个字典序最小的方案。

建出 `Trie` 树，令每个串的结点是 $v_1, v_2, \dots, v_n$。

因此，`LCP` 之和是：

$$
\sum_{i=1}^{n - 1} \text{dep}(\text{lca}(v_i, v_{i+1} )) \\
= \frac{1}{2} \sum_{i=1}^{n - 1} \text{dep}(v_i) + \text{dep}(v_{i+1}) - \text{dis}(v_i, v_{i+1}) \\
= \frac{1}{2} \text{dep}(v_1) + \frac{1}{2} \text{dep}(v_n) + \sum_{i=2}^{n - 1} \text{dep}(v_i) - \frac{1}{2} \sum_{i=1}^{n-1} \text{dis}(v_i, v_{i+1})
$$

式子有点奇怪，我们可给原本的集合加一个空串（根节点），令 $v_0=v_{n+1}=\varnothing$，因此上式变成：

$$
\sum_{i=1}^n \text{dep}(v_i) - \sum_{i=0}^{n} \text{dis}(v_i, v_{i+1})
$$

问题转变成成求后面部分的最大值。

一个推论：上面关键点长度和的最大值，等价于带权重心到每个关键点的距离和的两倍。

答案也很容易构造，将关键点按对于带权重心的所属子树染色，方案只要满足在 **环上** 相邻两个点不属于同一个组即可构造出方案。

上面这个条件满足，当且仅当众数大小小于等于总数除二的上取整。

最后，问题变成构造字典序最小的方案。显然，考虑一个贪心的过程，可以 $O(1)$ 检查选出后之后是否还能满足，由于最多只有 $|\Sigma|+1$ 组，对于每组排序后选取。注意一个细节，方案是一个环，还需要对于起点多检查一下。

<script>
  MathJax.Hub.Queue([Typeset, MathJax.Hub]);
</script>


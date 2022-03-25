---
mathjax: true
title: 2020-2021 Summer Petrozavodsk Camp Korean Contest
tags:
  - Trainings
categories:
  - 训练
date: 2021-03-18 22:57:52
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  7   |    6   |  O  |  .  |  .  |  O  |  O  |  O  |  O  |  .  |  .  |  .  |  O  |

<!--more-->

# A

Solved by XLor (02:52, +1).

显然是一个二分题。

考虑单组询问的暴力做法，我们一个接着一个在 $S$ 串上滑动，遇到空位则检查串长，直到找到最终位置。

但是，单组询问的复杂度是 $O(k)$ 的，因为有可能每次都是递归到最深处。

考虑离线所有询问，对询问点排序（归并），批量递归处理询问。此外，我们不需要暴力在 $S$ 串上滑动，预处理前缀的空位符个数，即可 $O(\log n)$ 的时间内二分出递归点。

# D

Solved by czswez and XLor (02:36, +4).

将数组划分为不降的段，先手玩家在数组内选择端点时，后手玩家因为要最大化因此一定是选择左右端点的最大值。因此，先手玩家的目标就是枚举数组中的每个点，最小化左右端点的最大值。观察到对于左端点，往右是递增的，对于右端点，往左是递增的，合并一下是一个单峰函数。函数只会在最值处出现阶梯状，其余一定是单调的。预处理一些前缀和，三分即可。

# E

Solved by miaojie (03:00, +1).

# F

Solved by XLor (04:55, +3).

显然有 $O(n^2k)$ 的 DP 做法，考虑决策单调性。

令 $f(i, j)$ 表示考虑到前 $i$ 个有 $j$ 个不选的最优解。

考虑对某个容量，观察决策点 $i+1$ 比 $i$ 优的话，后续就会一直是 $i+1$ 更优，抄一个整体二分的决策单调性。

注意，如果第二维是选的个数，不满足决策单调性，因为即使 $i+1$ 比 $i$ 优，后面因为个数的变化不一定会使用到这个信息。

# G

Solved by miaojie (00:40).

nim 博弈。

# K

Solved by miaojie and template (03:42, +3).

模板：求矩阵特征多项式。
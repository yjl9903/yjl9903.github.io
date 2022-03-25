---
mathjax: true
title: 2019 ICPC 沈阳现场赛
tags:
  - Trainings
categories:
  - 训练
date: 2019-11-20 09:58:03
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  30  |   5    |  O  |  .  |  .  |  O  |  !  |  .  |  .  |  O  |  .  |  !  |  !  |  O  |  O  |

<!--more-->

# A

Solved by Henry.

# C

UpSolved by Forsaken(?).

考虑每个位置为 good position 的贡献。

对于确定的 $k$，$ans=\sum_{m=1}^{n}lim^{n-m}f(m,k)$，其中 $f(m,k)$ 表示前 $m-1$ 个数至少 $k$ 个数比第 $m$ 个数小的方案数。

求 $f(m,k)$ :

- 令 $g(m,k)$ 为前 $m-1$ 个数恰好 $k$ 个数比第 $m$ 个数小的方案数，显然有 $g(m,k)=f(m,k)-f(m,k+1)$。

- 考虑枚举 $a_m=x$，利用算两次思想可得：$C(m-1,k)(x-1)^klim^{m-1-k}=\sum_{i=k}^{m-1}C(i,k)g(m,i,x)$,其中 $g(m,i,x)$ 表示前 $m-1$ 个数恰好有 $i$ 个比 $x$ 小的方案数，显然 $g(m,k)=\sum_{x=1}^{lim}g(m,k,x)$。

- 简单证明一下上述恒等式:等式左侧为前 $m-1$ 个数选 $k$ 个小于 $x$ 其他随意的方案数，显然有重复，右面则考虑每个前 $m-1$ 个恰好有 $i$ 个比 $x$ 小的方案会被算 $C(i,k)$ 次，证明完毕。

- 将 $x=1,2 ,\dots ,lim$ 带入上述恒等式再将得到所有恒等式相加可得：$C(m-1,k)lim^{m-1-k}S_k(lim-1)=\sum_{i=k}^{m-1}C(i,k)g(m,i)$，其中 $S_k(x)=\sum_{i=0}^{x}i^k$。

- 将 $g(m,i)=f(m,i)-f(m,i+1)$ 代入上式可得: $C(m-1,k)lim^{m-1-k}S_k(lim-1)=\sum_{i=k}^{m-1}C(i,k)(f(m,i)-f(m,i+1))$。

- 对上式利用 $C(n,m)=C(n-1,m)+C(n-1,m-1)$ 合并 $f(m,i)$ 的系数可得：$C(m-1,k)lim^{m-1-k}S_k(lim-1)=\sum_{i=k}^{m-1}C(i-1,k-1)f(m,i)$。

- 将上式的右式中的组合数拆开可得：$C(m-1,k)lim^{m-1-k}S_k(lim-1)=\sum_{i=k}^{m-1}f(m,i)\frac{(i-1)!}{(k-1)!(i-k)!}$.

- 整理上式可得：$C(m-1,k)lim^{m-1-k}S_k(lim-1)(k-1)!=\sum_{i=k}^{m-1}f(m,i)(i-1)!\frac{1}{(i-k)!}$,令$S(m,k)=C(m-1,k)lim^{m-1-k}S_k(lim-1)(k-1)!$，等式右面为2个差值为定值 $k$ 的多项式的卷积，所以上式从多项式角度看形式大概为 $S=F/C$，所以$F=S*C$，所以可得：$f(m,k)=\frac{1}{(k-1)!}\sum_{i=0}^kS(m,i)\frac{1}{(k-i)!}$.

将 $f(m,k)$ 带入 $ans$ 可得：对于确定的 $k$，$ans=\frac{1}{(k-1)!}\sum_{m=1}^{n}lim^{n-m}\sum_{i=0}^{k}S(m,i)\frac{1}{(k-i)!}$.

将 $S(m,i)=C(m-1,i)lim^{m-1-i}S_i(lim-1)(i-1)!$ 代入得：$ans=\frac{1}{(k-1)!}\sum_{m=1}^{n}lim^{n-m}\sum_{i=0}^k C(m-1,i)lim^{m-1-i}S_i(lim-1)(i-1)!\frac{1}{(k-i)!}$。

对 $ans$ 进行化简可得：$ans=\frac{1}{(k-1)!}lim^{n-1}\sum_{i=0}^{k}S_i(lim-1)lim^{-i}(i-1)!\frac{1}{(k-i)!}\sum_{m=1}^{n}C(m-1,i)$。

由恒等式 $\sum_{i=r}^{n}C(i,r)=C(n+1,r+1)$ 可化简 $ans=\frac{1}{(k-1)!}lim^{n-1}\sum_{i=0}^{k}S_i(lim-1)lim^{-i}(i-1)!C(n+1,i+1)\frac{1}{(k-i)!}$。

显然上式为一个和为定值 $k$ 的卷积形式，并且每一项除了 $S_i(lim-1)$ 都很容易求得.

而 $S_i(lim-1)=\sum_{x=0}^{lim-1}x^i$，根据伯努利数可知 $S_i(lim-1)=\frac{1}{i+1}\sum_{j=0}^{i}(-1)^j C(i+1,j)B_j(lim-1)^{i+1-j}$，显然右式为2个和为定值$i$的卷积形式，所以只要知道了伯努利数做一次 $ntt$ 就可以求出所有的 $S_i(lim-1)$。

伯努利数的指数生成函数：$\sum_{i=0}^{\infty}B_i\frac{x^i}{i!}=\frac{x}{e^x-1}$，所以多项式求逆即可求出所有的伯努利数.

求所有 $S_i(lim-1)$ 后，由于 $ans$ 也是一个卷积形式，做一次 $ntt$ 就可以求出所有的 $ans$ 。

# D

Solved by Forsaken.

# E

UnSolved by Henry.

二分出包含每个点的线段，排序后扫描线即可。

可惜不能二分，必须用反演直接算出线段。

# H

Solved by Forsaken.

# J

UnSolved by XLor.

线段树分治。

可是不知道询问怎么批量做。

# K

UnSolved by Henry and Forsaken.

整数划分。

可是没带五边形的板子。

# L

Solved by Henry and Forsaken.

二分答案。

# M

Solved by XLor

回文树上 $\log$ 段等差数列 $border$ 的 $dp$。

可是被 ** 出题人出成了大水题呢。

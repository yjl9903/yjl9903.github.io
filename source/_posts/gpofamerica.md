---
mathjax: true
title: XIX Open Cup named after E.V. Pankratiev. Grand Prix of America
tags:
  - Trainings
  - Probability
  - Tree
  - DP
  - Poly
categories:
  - 训练
date: 2019-09-25 23:28:26
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  M  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  87  |   7    |  O  |  .  |  .  |  O  |  Ø  |  Ø  |  O  |  .  |  !  |  O  |  .  |  Ø  |

<!--more-->

# A

Solved by Henry and Forsaken.

凸包面积 + 期望。

注意精度问题。

# D

Solved by Henry and Forsaken.

类欧模板题。

# F

UpSolved by XLor.

概率 dp + 分段多项式 PDF。

记 $f_u(x)$ 表示点 $u$ 的概率分布函数，有下转移方程：
$$
f_u(x)=\frac{1}{b_u} \int_{0}^{x} \prod_{v \in son(u)} (1-f_v(t)) dt
$$
若点 $x$ 是叶子结点，则其概率分布函数为：
$$
f_u(x)=\begin{cases}
\frac{x}{b_u} & x \in [0,b_u] \\
0 & others
\end{cases}
$$
注意到这个多项式是有范围的，考虑使用 PDF 维护这个多项式，实现细节见代码。

为了便于实现，将值域取了相反数。

评论：分段多项式不会维护，自闭 T^T。

# G

Solved by XLor.

扫描线扫一下。

# I

UnSolved by XLor and Forsaken.

假算法：开头尽量选后缀最大字母，最后一次特判选最大后缀。

# J

Solved by XLor.

序列自动机。
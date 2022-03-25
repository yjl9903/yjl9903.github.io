---
mathjax: true
title: 2020 牛客暑期多校训练营第 9 场
tags:
  - Trainings
categories:
  - 训练
  - 牛客多校
date: 2020-08-08 20:45:31
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  38  |   9    |  O  |  O  |  Ø  |  .  |  O  |  O  |  .  |  Ø  |  O  |  O  |  O  |  .  |

<!--more-->

# A

Solved by XLor (00:07:13).

```python
print(eval(input().replace('(', '**(')))
```

# B

Solved by XLor, miaojie and ytriayggg (04:34:24, +5).

# C

UpSolved by ytriayggg.

枚举每一对算贡献，线段树优化 DP。

# E

Solved by ytriayggg (01:07:35, +3).

# F

Solved by miaojie (01:51:24, +4).

尺取（双指针）。

# I 

Solved by miaojie (00:28:40, +1).

拿出一个数字，特判前导 $0$。

# J

Solved by XLor (02:58:22, +1).

枚举上边界，枚举下边界，枚举每个右端点，判断时需要保证前缀和的差为 $0,1,-1$，并且左右被黑框包围。注意到，每个点的贡献范围是一个区间，在结束时删除贡献即可。

# K

Solved by XLor (00:50:46).

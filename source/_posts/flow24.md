---
mathjax: true
title: 网络流 24 题
date: 2019-03-20 20:45:25
tags:
- Graph
- Network-Flow
categories:
- 图论
- 网络流
---

# 搭配飞行员

二分图匹配。

# 太空飞行计划

最大权闭合子图。

闭合子图：子图中每个点的出边指向的结点也在子图内。

有 $n$ 个商品，$m$ 个物资，生产一个商品需要某些物资，商品有价值，物资有费用（出现时只算一次），求最大利益。

建一个超级源点 $S$ 指向所有商品，容量为商品权值，建一个超级汇点 $T$，所有物资指向 $T$，容量为物资费用。

对于依赖关系，建一条容量为无穷大的边。

最大权闭合子图为商品正权值之和减去最大流（最小割）。

最大权闭合子图为最后所有与超级源点相连的点。

<!--more-->

# 代码

## 太空飞行计划

```c++
scanf("%d%d", &m, &n);
int sum = 0;
for (int i = 1, x; i <= m; i++) {
    scanf("%d", &x);
    sum += x;
    add(n + m + 1, i, x);
    cin.getline(tmp, 2000);
    stringstream ss(tmp);
    while (ss >> x)  {
        add(i, m + x, inf);
    }
}
for (int i = 1, x; i <= n; i++) {
    scanf("%d", &x);
    add(m + i, n + m + 2, x);
}
int ans = sum - dinic(n + m + 1, n + m + 2);
for (int i = 1, flag = 0; i <= m; i++) if (dep[i]) {
    if (flag) putchar(' ');
    printf("%d", i); flag = 1;
}
puts("");
for (int i = m + 1, flag = 0; i <= n + m; i++) if (dep[i]) {
    if (flag) putchar(' ');
    printf("%d", i - m);
    flag = 1;
}
printf("\n%d", ans);
```
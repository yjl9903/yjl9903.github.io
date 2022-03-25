---
mathjax: true
title: 墨墨的等式
date: 2019-03-19 20:55:13
tags:
- Graph
- Number Theory
categories:
- 题解
---

感谢 <span style="color:black">E</span><span style="color:red">llery</span> 贡献的题解。

# 题面

给定系数序列 $a$ 和 $B$ 的范围 $[mn,mx]$，求不定方程

$$
a_1 \cdot x_1 + a_2 \cdot x_2 + a_3 \cdot x_3 + \dots + a_n \cdot x_n = B
$$

的非负整数解数。

其中 $1 \le n \le 12, 0 \le a_i \le 5 \times 10^5, 1 \le B \le 10^{12}$。

# 分析

先任意取一 $a_i$，当我们找到一个符合条件的 $B$ 有 $B \text{ mod } ai = d $ 时，因为符合条件所以有一组整数解 $(x_1,x_2,x_3,\dots,x_i,\dots,x_n)$。

此时 $B+a_i$ 必定也是符合条件的，这个时候的整数解会变成 $(x_1,x_2,x_3,\dots,x_i+1,\dots,x_n)$。

所以我们对于每一个余数 $0,1,2,3,\dots,\min(a)-1$，只需要找到符合条件的最小的B即可。

在余数 $[0,\min(a)-1]$ 上建立点集，用 $dist[i]$ 来表示模 $min(a)$ 的余数为 $i$ 的最小 $B$ 值。

为了求出 $dist$ 数组，考虑 dijkstra 的松弛操作，对于 $i$，加上其他任意一个 $a_j$ 时，会出现一个新的余数 $k = (i + a_j) \text{ mod } \min(a)$，可以理解为由点 $i$ 向点 $k$ 连接了边权为 $a_j$ 的边。

对于每个点 $i$ 都有了一个 $dist[i]$。如果 $dist[i]$ 大于 $B$，则其对答案是没有贡献的；若小于则对答案贡献为 $(B – dist[i]) / \min(a) + 1$。

区间前缀和差分一下得到最终答案。

<!--more-->

# 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#include <queue>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 500000 + 5;

struct node {
    int to; ll d;
    bool operator<(const node& b) const {
        return d > b.d;
    }
};

int n, mn = 1e9, vis[maxn];
ll bmn, bmx, dis[maxn];
vector<int> a;

ll cal(ll x) {
    ll ans = 0;
    for (int i = 0; i < mn; i++) {
        if (x < dis[i]) continue;
        ans += (x - dis[i]) / mn + 1;
    }
    return ans;
}

int main() {
    scanf("%d%lld%lld", &n, &bmn, &bmx);
    for (int i = 1, x; i <= n; i++) {
        scanf("%d", &x);
        if (!x) continue;
        a.push_back(x); mn = min(mn, x);
    }
    for (int i = 1; i <= mn; i++) dis[i] = 1e18;
    priority_queue<node> q; q.push({0, 0}); dis[0] = 0;
    while (!q.empty()) {
        node tp = q.top(); q.pop();
        if (vis[tp.to]) continue;
        vis[tp.to] = 1;
        for (int& v: a) {
            int y = (v + tp.to) % mn;
            if (dis[y] > dis[tp.to] + v) {
                dis[y] = dis[tp.to] + v;
                q.push({y, dis[y]});
            }
        }
    }
    cout << cal(bmx) - cal(bmn - 1) << endl;
    return 0;
}
```
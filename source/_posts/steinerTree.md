---
mathjax: true
title: 斯坦纳树
date: 2019-02-12 00:10:20
tags:
- Tree
- Graph
categories:
- 树
---

给定带权图 $G$，求让 $k$ 个关键点联通的权值最小的子图（$1 \le k \le 10$）。

关键点的个数很少，考虑状压 $dp$。

设 $dp[mask][i]$ 表示关键点的点集 $mask$ 连到顶点 $i$ 最小权值和。

转移的时候有两种情况：

1. 固定顶点 $i$，合并 $mask$ 的子集 $T$ 和 $T$ 的补集两个 $dp$ 状态，相当于顶点分叉。

2. 固定点集 $mask$，顶点 $i$ 向周围的点用最短路进行转移，相当于节点向外生长。

时间复杂度：$O(3^k \cdot n+2^k \cdot m \log n)$。

<!--more-->

# HDu3311 Dig The Wells

$n$ 座寺庙，$m$ 个其他地点，这 $n+m$ 个地点可以花费 $c_i$ 修井，地点之间连有带权的边，求让所有寺庙都和井联通的最小权值。

建一个超级源点，连到所有点上，权值为修井的花费，因此就是关键点为 $n$ 座寺庙和超级源点的最小斯坦纳树。

# 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <utility>
#include <queue>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const ll inf = 1ll << 60;
const int mod = 998244353;
const int maxn = 1010 + 5;

bool vis[1 << 6][maxn];
struct node{
    int to; ll d;
    bool operator< (const node& b)const{
        return d > b.d;
    }
};

vector<PII> edge[maxn];
int n, m, p, a[maxn];
ll dp[1 << 6][maxn];

int main() {
    while (scanf("%d%d%d", &n, &m, &p) == 3) {
        ms(vis, 0);
        for (int i = 0; i <= n + m; i++) edge[i].clear();
        for (int i = 1; i <= n + m; i++) {
            scanf("%d", a + i);
            edge[0].push_back({i, a[i]});
            edge[i].push_back({0, a[i]});
        }
        for (int i = 1, u, v, w; i <= p; i++) {
            scanf("%d%d%d", &u, &v, &w);
            edge[u].push_back({v, w});
            edge[v].push_back({u, w});
        }
        int ss = 1 << (n + 1);
        for (int s = 1; s < ss; s++) for (int u = 0; u <= n + m; u++) dp[s][u] = inf;
        for (int u = 0; u <= n; u++) dp[1 << u][u] = 0;
        priority_queue<node> pq;
        for (int s = 1; s < ss; s++) {
            for (int u = 0; u <= n + m; u++) {
                for (int t = s; t; t = (t - 1) & s) {
                    dp[s][u] = min(dp[s][u], dp[t][u] + dp[s ^ t][u]);
                }
            }
            for (int u = 0; u <= n + m; u++) if (dp[s][u] != inf) pq.push({u, dp[s][u]});
            while (!pq.empty()) {
                node tp = pq.top(); pq.pop();
                if (vis[s][tp.to]) continue;
                vis[s][tp.to] = 1;
                for (auto& x: edge[tp.to]) {
                    if (dp[s][x.first] > dp[s][tp.to] + x.second) {
                        dp[s][x.first] = dp[s][tp.to] + x.second;
                        pq.push({x.first, dp[s][x.first]});
                    }
                }
            }
        }
        ll ans = inf;
        for (int i = 0; i <= n + m; i++) ans = min(ans, dp[ss - 1][i]);
        printf("%lld\n", ans);
    }
    return 0;
}
```
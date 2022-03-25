---
mathjax: true
title: 边双联通分量缩点
date: 2019-04-14 22:29:19
tags:
- Graph
categories:
- 图论
- 连通性
---

注意不要忘记原点变成**缩点后的新点**。

# 模板

```c++
int cnt, bel[maxn]; // important!
namespace Tarjan {
    int tot, dfn[maxn], low[maxn], st[maxn], top, vis[maxn];
    void clear(int n) {
        tot = top = cnt = 0;
        for (int i = 1; i <= n; i++) {
            edge[i].clear(); dfn[i] = vis[i] = bel[i] = 0;
        }
    }
    void dfs(int u, int f) {
        dfn[u] = low[u] = ++tot;
        st[++top] = u; vis[u] = 1;
        int k = 0;
        for (int& v: edge[u]) {
            if (v == f && !k) {
                k++; continue;
            } 
            if (!dfn[v]) {
                dfs(v, u); low[u] = min(low[u], low[v]);
            } else if (vis[v]) low[u] = min(low[u], dfn[v]);
        }
        if (dfn[u] == low[u]) {
            cnt++; int t = 0;
            do {
                t = st[top--];
                bel[t] = cnt;
                vis[t] = 0;
            } while (t != u);
        }
    }
    void scc(int n, vector<int> * g) {
        for (int i = 1; i <= n; i++) if (!dfn[i]) Tarjan::dfs(i, 0);
        for (int i = 1; i <= n; i++) g[i].clear();
        for (int i = 1; i <= n; i++) {
            int u = bel[i];
            for (int& x: edge[i]) {
                int v = bel[x];
                if (u != v) {
                    g[u].push_back(v);
                }
            }
        }
    }
}
```

<!--more-->
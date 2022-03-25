---
mathjax: true
title: 支配树
date: 2019-07-29 23:52:45
tags:
- Tree
- Graph
categories:
- 树
---

# 定义

- `semi[x]` 半必经点： $x$ 的祖先 $z$ 中，能不经过 $z$ 和 $x$ 之间的树上的点而到达 $x$ 的点中深度最小的。
- `idom[x]` 最近必经点：就是深度最大的根到 $x$ 的必经点。

其它的证明看不懂 T^T。

# 模板

```c++
vector<int> G[maxn], rG[maxn];
vector<int> dt[maxn];
// 原图，反图，支配树

namespace DomTree {
    int fa[maxn], idx[maxn], tot, ridx[maxn];
    int c[maxn], best[maxn], semi[maxn], idom[maxn];
    void clear(int n) {
        tot = 0;
        fill(c, c + n + 1, -1);
        fill(idx, idx + n + 1, 0);
        for (int i = 0; i <= n; i++) {
            dt[i].clear();
            semi[i] = best[i] = i;
        }
    }
    void dfs(int u) {
        idx[u] = ++tot; ridx[tot] = u;
        for (int& v: G[u]) {
            if (idx[v]) continue;
            fa[v] = u; dfs(v);
        }
    }
    int fix(int x) {
        if (c[x] == -1) return x;
        int &f = c[x], rt = fix(f);
        if (idx[semi[best[x]]] > idx[semi[best[f]]]) best[x] = best[f];
        return f = rt;
    }
    void build(int rt) {
        dfs(rt);
        for (int i = tot; i >= 1; i--) {
            int x = ridx[i], mn = tot + 1;
            for (int& u: rG[x]) {
                if (!idx[u]) continue;
                fix(u); mn = min(mn, idx[semi[best[u]]]);
            }
            c[x] = fa[x];
            dt[semi[x] = ridx[mn]].push_back(x);
            x = ridx[i - 1];
            for (int& u: dt[x]) {
                fix(u);
                if (semi[best[u]] != x) idom[u] = best[u];
                else idom[u] = x;
            }
            dt[x].clear();
        }
        for (int i = 2; i <= tot; i++) {
            int u = ridx[i];
            if (idom[u] != semi[u]) idom[u] = idom[idom[u]];
            dt[idom[u]].push_back(u);
        }
    }
}
```

<!--more-->
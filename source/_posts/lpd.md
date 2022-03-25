---
mathjax: true
title: 长链剖分
date: 2019-06-06 23:35:22
tags:
- Tree
- DP
categories:
- 树
---

# 性质一

所有长链的长度和是 $\mathcal{O}(n)$ 的。

# 性质二

一个点的 $k$ 级祖先所在重链长度至少为 $k$。


# $\mathcal{O}(n\log{}n)$ - $\mathcal{O}(1)$ 在线询问 $k$ 级祖先

首先预处理出倍增数组，再预处理每个数的最高位在哪，再预处理每条重链的端点长度为 $l$，顶点往上走 $l$ 步和沿着重链走 $l$ 步是哪些点。

对于 $k$ 级祖先，令 $r=highbit(k)$，显然 $r>k-r$，用倍增数组将询问点向上跳 $r$ 步到 $y$ 点。

由性质二，$y$ 所在链长大于等于 $r > k - r$。

最坏情况下，$y$ 就是一个重链顶点，由于其预处理了往上走至少 $r$ 步的点，因此可以直接回答，否则重链顶点在 $y$ 到答案的路径上，显然也可以直接回答。

第二种情况，$y$ 的重链端点是答案的祖先，那么显然 $y$ 和答案在一条重链内，也可以直接回答。

# $\mathcal{O}(n)$ 统计每个点子树中以深度为下标的可合并信息

重儿子继承父亲的信息，由于信息是以深度为下标，因此只需数组指针移位。

轻儿子暴力将信息与父亲合并。

复杂度是 $\mathcal{O}(\sum\text{重链长度})$，即 $\mathcal{O}(n)$。

<!--more-->

# 模板

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#ifdef XLor
  #define dbg(args...) do {cout << #args << " -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

int n, m;
vector<int> edge[maxn];

int dep[maxn], son[maxn], top[maxn], len[maxn], maxd[maxn], fa[maxn][20];
void dfs(int u, int f) {
    maxd[u] = dep[u] = dep[f] + 1;
    fa[u][0] = f; son[u] = 0;
    for (int i = 1; i < 20; i++) fa[u][i] = fa[fa[u][i - 1]][i - 1];
    for (int v: edge[u]) {
        if (v == f) continue;
        dfs(v, u);
        if (maxd[v] > maxd[son[u]]) son[u] = v, maxd[u] = maxd[v];
    }
}
void dfs(int u, int f, int tp, int l) {
    top[u] = tp; len[u] = l;
    if (son[u]) {
        dfs(son[u], u, tp, l + 1);
        len[u] = len[son[u]];
    }
    for (int& v: edge[u]) {
        if (v == f || v == son[u]) continue;
        dfs(v, u, v, 1);
    }
}
bool vis[maxn]; int highbit[maxn];
vector<int> up[maxn], dwn[maxn];
void init() {
    dfs(1, 0); dfs(1, 0, 1, 1);
    for (int i = 1; i <= n; i++) {
        int tp = top[i];
        if (!vis[tp]) {
            vis[tp] = 1;
            int l = 0, now = tp;
            while (l < len[tp] && now) {
                now = fa[now][0];
                l++; up[tp].push_back(now);
            }
            l = 0, now = tp;
            while (l < len[tp]) {
                now = son[now];
                l++; dwn[tp].push_back(now);
            }
        }
    }
    int mx = 1;
    for (int i = 1; i <= n; i++) {
        if (i >> mx & 1) mx++;
        highbit[i] = mx - 1;
    }
}
int qkth(int u, int k) {
    if (k > dep[u]) return 0;
    if (k == 0) return u;
    u = fa[u][highbit[k]]; k -= 1 << highbit[k];
    if (k == 0) return u;
    if (dep[u] - dep[top[u]] == k) return top[u];
    if (dep[u] - dep[top[u]] > k) return dwn[top[u]][dep[u] - dep[top[u]] - k - 1];
    return up[top[u]][k - (dep[u] - dep[top[u]]) - 1];
}

int main() {
    scanf("%d", &n);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v); edge[v].push_back(u);
    } init();
    scanf("%d", &m);
    int u, k, lastans = 0;
    while (m--) {
        scanf("%d%d", &u, &k);
        u ^= lastans; k ^= lastans;
        printf("%d\n", lastans = qkth(u, k));
    }
    return 0;
}
```

# POI2014 Hot Hotels

$f(i,j)$ 表示子树 $i$ 内距离 $i$ 为 $j$ 的点数。

$g(i,j)$ 表示子树 $i$ 内，满足两个点距离 $i$ 为 $2d-j$， LCA 距离 $i$ 为 $d-j$ 的点对数。

## 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n; ll ans = 0;
vector<int> edge[maxn];

int dep[maxn], son[maxn], top[maxn], len[maxn];
void dfs1(int u, int f) {
    len[u] = 0;
    for (int v: edge[u]) {
        if (v == f) continue;
        dfs1(v, u);
        if (len[v] > len[son[u]]) son[u] = v, len[u] = len[v];
    }
    len[u]++;
}
int tmp[maxn * 8], *f[maxn], *g[maxn], *tot = tmp;
void make(int u, int len) {
    f[u] = tot; tot += (len + 5) * 2;
    g[u] = tot; tot += (len + 5) * 2;
}
void dfs(int u, int ff) {
    if (son[u]) {
        f[son[u]] = f[u] + 1;
        g[son[u]] = g[u] - 1;
        dfs(son[u], u);
    }
    f[u][0] = 1;
    ans += g[u][0];
    for (int& v: edge[u]) {
        if (v == ff || v == son[u]) continue;
        make(v, len[v]);
        dfs(v, u);
        for (int j = 0; j <= len[v] + 1; j++) {
            ans += f[u][j] * g[v][j + 1];
            if (j) ans += g[u][j] * f[v][j - 1];
            g[u][j] += g[v][j + 1];
            if (j) g[u][j] += f[u][j] * f[v][j - 1];
            if (j) f[u][j] += f[v][j - 1];
        }
    }
}

int main() {
    scanf("%d", &n);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v); edge[v].push_back(u);
    }
    dfs1(1, 0);
    make(1, len[1]);
    dfs(1, 0);
    cout << ans << '\n';
    return 0;
}
```
---
mathjax: true
title: 基于变换合并的树上动态 DP
date: 2019-06-30 17:01:35
tags:
- Tree
- DP
- Data Structure
categories:
- 树
---

# 动态最大独立集

显然有 $dp(u,0/1)$ 表示点 $u$ 是否选，转移显然。

把答案拆成两部分，只选取轻儿子的 $dp$ 值 $g(u,0/1)$ 和本来的 $dp$ 值 $f(u,0/1)$。

对于点 $u$ 的**重儿子** $v$，可以列出转移方程：

$$
f(u,0)=g(u,0)+max(f(v,0),f(v,1))=max(g(u, 0)+f(v, 0), g(u,0) + f(v, 1)) \\
f(u,1)=g(u,1)+f(v,0)
$$

定义一个广义的矩阵乘法，加法改为取 $\max$，乘法改为加法，则有转移矩阵：
$$
\begin{bmatrix}
g(u,0) & g(u, 0) \\
g(u,1) & -\infty
\end{bmatrix}
\begin{bmatrix}
f(v,0)  \\
f(v,1) 
\end{bmatrix}
=
\begin{bmatrix}
f(u,0)  \\
f(u,1) 
\end{bmatrix}
$$
这个 $dp$ 现在可以改成用树链剖分维护每个点只选取轻儿子 $dp$ 值的矩阵。

一个点的 $dp$ 值，可以通过从该点重链的叶子结点往上转移的方式得到。

对于修改操作，一个点所处的重链均不需要修改，只需要修改重链的链头的父亲，记录一下修改前后的链头变化，更新一下这个父亲的矩阵，再通过重链往上爬。

单次操作复杂度：$\mathcal{O}(8\log^2n)$。

<!--more-->

## 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#ifdef XLor
  #define dbg(args...) do { cout << #args << " -> "; err(args); } while (0)
  void err() { std::cout << std::endl; }
  template<typename T, typename...Args>
  void err(T a, Args...args) { std::cout << a << ' '; err(args...); }
#else
  #define dbg(...)
#endif
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

struct Mat {
    static const int M = 2;
    int a[M][M];
    Mat() { for (int i = 0; i < M; i++) for (int j = 0; j < M; j++) a[i][j] = -inf; }
    void clear() { ms(a, 0); }
    void eye() { for (int i = 0; i < M; i++) a[i][i] = 1; }
    int* operator [] (int x) { return a[x]; }
    const int* operator [] (int x) const { return a[x]; }
    Mat operator * (const Mat& b) {
        const Mat& a = *this; Mat r;
        for (int i = 0; i < M; i++)
            for (int j = 0; j < M; j++)
                for (int k = 0; k < M; k++)
                    r[i][j] = max(r[i][j], a[i][k] + b[k][j]);
        return r;
    }
} b[maxn << 2], val[maxn];

int n, q, a[maxn];
vector<int> edge[maxn];

int dep[maxn], siz[maxn], son[maxn], fa[maxn], top[maxn];
void dfs(int u, int f) {
    fa[u] = f;
    dep[u] = dep[f] + 1; 
    siz[u] = 1;
    int m = -1;
    for (int& v: edge[u]) {
        if (v == f) continue;
        dfs(v, u);
        siz[u] += siz[v];
        if (siz[v] > m) m = siz[v], son[u] = v;
    }
}
int f[maxn][2], g[maxn][2];
int id[maxn], mp[maxn], ed[maxn], tot;
void dfs(int u, int ff, int tp) {
    g[u][1] = a[u];
    id[u] = ++tot; mp[tot] = u;
    top[u] = tp; ed[tp] = tot;
    if (!son[u]) {
        // ???
        f[u][1] = a[u]; 
        return ;
    }
    dfs(son[u], u, tp);
    f[u][0] += max(f[son[u]][0], f[son[u]][1]);
    f[u][1] += f[son[u]][0];
    for (int& v: edge[u]) {
        if (v == ff || v == son[u]) continue;
        dfs(v, u, v);
        g[u][0] += max(f[v][0], f[v][1]);
        g[u][1] += f[v][0];
    }
    f[u][0] += g[u][0];
    f[u][1] += g[u][1];
}

#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
void pushup(int rt) {
    b[rt] = b[rt << 1] * b[rt << 1 | 1];
}
void build(int l, int r, int rt) {
    if (l == r) {
        int u = mp[l];
        val[u][0][0] = val[u][0][1] = g[u][0];
        val[u][1][0] = g[u][1];
        val[u][1][1] = -inf;
        b[rt] = val[u];
        return ;
    }
    int m = (l + r) / 2;
    build(lson); build(rson);
    pushup(rt);
}
void update(int i, int l, int r, int rt) {
    if (l == r) {
        b[rt] = val[mp[l]]; return ;
    }
    int m = (l + r) / 2;
    if (i <= m) update(i, lson);
    else update(i, rson);
    pushup(rt);
}
Mat query(int L, int R, int l, int r, int rt) {
    if (L <= l && r <= R) return b[rt];
    int m = (l + r) / 2; 
    if (R <= m) return query(L, R, lson);
    if (L > m) return query(L, R, rson);
    return query(L, R, lson) * query(L, R, rson);
}

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v); edge[v].push_back(u);
    }
    dfs(1, 0); dfs(1, 0, 1); build(1, n, 1);
    int x, y;
    while (q--) {
        scanf("%d%d", &x, &y);
        val[x][1][0] += y - a[x]; a[x] = y;
        while (x) {
            int tp = top[x];
            Mat last = query(id[tp], ed[tp], 1, n, 1);
            update(id[x], 1, n, 1);
            Mat nw = query(id[tp], ed[tp], 1, n, 1);
            x = fa[tp];
            val[x][0][0] += max(nw[0][0], nw[1][0]) - max(last[0][0], last[1][0]);
            val[x][0][1] = val[x][0][0];
            val[x][1][0] += nw[0][0] - last[0][0];
        }
        Mat ans = query(id[1], ed[1], 1, n, 1);
        printf("%d\n", max(ans[0][0], ans[1][0]));
    }
    return 0;
}
```


---
title: 最近公共祖先
date: 2018-09-24 21:53:29
tags:
- Tree
- Graph
categories:
- 树
---

# 倍增LCA

```c++
int head[maxn], to[maxn * 2], nxt[maxn * 2], d[maxn * 2], tot;
void add(int x, int y, int w){
    to[++tot] = y; nxt[tot] = head[x]; d[tot] = w; head[x] = tot;
}

int n, m;

int dp[maxn][20], dep[maxn], dis[maxn];
void dfs(int u, int fa){
    dp[u][0] = fa; dep[u] = dep[fa] + 1;
    for (int i = head[u]; i; i = nxt[i]){
        int v = to[i];
        if (v == fa) continue;
        dis[v] = dis[u] + d[i];
        dfs(v, u);
    }
}
void init(){
    ms(dp, 0); dep[0] = dis[0] = 0;
    dfs(1, 0); 
    for (int j = 1; j < 20; j++) 
        for (int i = 1; i <= n; i++) 
            dp[i][j] = dp[dp[i][j - 1]][j - 1];
}
int lca(int x, int y){
    if (dep[x] < dep[y]) swap(x, y);
    int tmp = dep[x] - dep[y];
    for (int i = 0; tmp; i++, tmp >>= 1)
        if (tmp & 1) x = dp[x][i];
    if (x == y) return x;
    for (int i = 19; i >= 0; i--){
        if (dp[x][i] != dp[y][i]){
            x = dp[x][i]; y = dp[y][i];
        }
    }
    return dp[x][0];
}
```

# 树链剖分LCA

```c++
namespace hld {
    int siz[maxn], dep[maxn], fa[maxn], son[maxn], top[maxn];
    void dfs(int u, int f) {
        dep[u] = dep[f] + 1; fa[u] = f; siz[u] = 1; son[u] = 0;
        int m = -1;
        for (auto& v: edge[u]) {
            if (v == f) continue;
            dfs(v, u);
            siz[u] += siz[v];
            if (siz[v] > m) son[u] = v, m = siz[v];
        }
    }
    void dfs(int u, int f, int tp) {
        top[u] = tp;
        if (!son[u]) return;
        dfs(son[u], u, tp); // !
        for (auto& v: edge[u]) {
            if (v == f || v == son[u]) continue; // !
            dfs(v, u, v);
        }
    }
    void build() {
        dfs(1, 0); dfs(1, 0, 1);
    }
    int qlca(int u, int v) {
        while (top[u] != top[v]){
            if (dep[top[u]] < dep[top[v]]) swap(u, v);
            u = fa[top[u]];
        }
        return dep[u] < dep[v] ? u : v;
    }
}
```
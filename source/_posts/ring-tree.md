---
mathjax: true
title: 基环树
date: 2019-07-07 08:18:57
tags:
- Tree
- DP
categories:
- 树
---

$n$ 个点 $n$ 条边的联通图是一棵基环树，边由 $n-1$ 条树边加上一条环边组成。

# Trick

+ 拆出一条环边，剩余当成树做。

+ 抠出整个环，环上每个点挂了一棵子树，先做 Tree dp，在环上做序列 dp。

<!--more-->

# IOI 2018 islands

求基环树森林的直径总和。

抠出每个联通块的环，环上每棵子树求最大深度和子树直径。

考虑 $dp[i]$ 表示到 $i$ 的最大长度，转移就是 $dp[i]=depth[i]+\max \{ depth[j]+distance(i,j) \}$，

将环补成链，$dp[i]=depth[i]+distance(i)+\max \{ depth[j]-distance(j) \}$，$j$ 有一个取值范围，单调队列优化转移即可。

# 2018 牛客多校第二场 B discount

$n$ 个物品，价格为 $p_i$，购买一个物品可以使用两种优惠，价格减 $d_i$ 和 免费获得一个 $f_i$，求拥有所有物品的最小花费。

第二种优惠组成了一个基环内向树，并查集找到每个联通块的环边。

对于环上每个子树做一个 Tree dp。

记 $dp[u][0/1]$，$dp[u][0]$ 表示 $u$ 使用优惠的最小花费，$dp[u][1]$ 表示购买 $u$ 的最小花费。

在环上 dp 时，断环为链，讨论一下环上最后一个点对第一个的影响，转化为序列上的 dp。

## 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#include <cassert>
#ifdef XLor
  #define dbg(args...) do { std::cout << #args << " -> "; err(args); } while (0)
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
const ll inf = 1ll << 62;
const int maxn = 100000 + 5;

struct DSU {
    int pre[maxn];
    void init(int n) {
        for (int i = 1; i <= n; i++) pre[i] = i;
    }
    int find(int x) {
        return x == pre[x] ? x : pre[x] = find(pre[x]);
    }
    int join(int x, int y) {
        x = find(x); y = find(y);
        if (x == y) return 0;
        pre[x] = y;
        return 1;
    }
} f;

vector<int> edge[maxn];
int n, p[maxn], d[maxn], fa[maxn];

vector<int> onring;
int ring[maxn], vis[maxn], ok[maxn];
void getring(int u) {
    vis[u] = ok[u] = 1;
    for (int v: edge[u]) {
        if (vis[v]) {
            if (!onring.empty()) continue;
            int x = u;
            while (x != v) onring.push_back(x), x = fa[x];
            onring.push_back(v);
        }
        getring(v);
    }
    vis[u] = 0;
}
ll dp[maxn][2];
// dp[u][0] -> get all the things
// dp[u][1] -> get all the things and buy u
void dfs(int u) {
    ll sum = 0;
    for (int v: edge[u]) {
        if (ring[u] && ring[v]) continue;
        dfs(v);
        sum += dp[v][0];
    }
    dp[u][1] = sum + p[u];
    dp[u][0] = p[u] - d[u] + sum;
    for (int v: edge[u]) {
        if (ring[u] && ring[v]) continue;
        dp[u][0] = min(dp[u][0], dp[v][1] + sum - dp[v][0]);
    }
}

int main() {
    scanf("%d", &n); f.init(n);
    for (int i = 1; i <= n; i++) scanf("%d", p + i);
    for (int i = 1; i <= n; i++) scanf("%d", d + i);
    vector<int> roots;
    ll ans = 0;
    for (int i = 1; i <= n; i++) {
        scanf("%d", fa + i); edge[fa[i]].push_back(i);
        if (!f.join(i, fa[i])) roots.push_back(i);
    }
    for (int rt: roots) {
        onring.clear(); getring(rt);
        for (int x: onring) ring[x] = 1;
        vector<ll> f, g, h;
        for (int x: onring) {
            dfs(x); 
            f.push_back(dp[x][0]);
            g.push_back(dp[x][1]);
            h.push_back(dp[x][1] - p[x]);
        }
        int sz = (int)onring.size();
        if (sz == 1) {
            ans += f[0]; continue;
        }
        ll res = inf;
        vector< pair<ll,ll> > dp(sz, {inf, inf});
        dp[0].first = f[0]; dp[0].second = g[0];
        for (int i = 1; i < sz; i++) {
            dp[i].second = g[i] + dp[i - 1].first;
            dp[i].first = min(dp[i - 1].second + h[i], f[i] + dp[i - 1].first);
        }
        res = dp[sz - 1].first;
        dp[0].first = h[0]; dp[0].second = g[0];
        for (int i = 1; i < sz; i++) {
            dp[i].second = g[i] + dp[i - 1].first;
            dp[i].first = min(dp[i - 1].second + h[i], f[i] + dp[i - 1].first);
        }
        res = min(res, dp[sz - 1].second);
        ans += res;
    }
    cout << ans;
    return 0;
}
```
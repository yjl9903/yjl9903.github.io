---
mathjax: true
title: Crash 的文明世界 题解
date: 2019-08-11 00:35:45
tags:
- Tree
- DP
- Math
- Combinatorial
categories:
- 树
---

# 题面

给定一棵无根树，对每个点 $i$，求 $S(u)=\sum_{i=1}^n dis(u,i)^k$，$dis(u,i)$ 表示树上路径长度。

其中 $3\le n \le 5\cdot 10^4, 1\le k \le 200$。

# 分析

## 做法一

斯特林数展开。

$$
S(u)=\sum_{i=1}^n\sum_{j=0}^k S(k,j) dis(u,i)^{\underline j}=\sum_{j=0}^k S(k,j)\sum_{i=1}^n dis(u,i)^{\underline j}
$$

其中 $n^{\underline m}=P(n,m)$ 表示 $n$ 的 $m$ 次下降幂。

记 $dp(u,i)$ 表示 $u$ 的子树下所有点对 $i$ 次下降幂的贡献，即 $\sum_{v \in subtree(u)} dis(u,v)^{\underline i}$。

转移就是考虑恒等式 $n^{\underline m}=(n-1)^{\underline m}+m\cdot n^{\underline m-1}$，做两遍 up and down 的树形 dp 即可。


## 做法二

$dp(u,i)$ 直接维护出 $u$ 的子树内所有点到 $u$ 的距离的 $i$ 次方之和。

转移就是考虑 $(x+y)^k$ 的二项式展开即可。

<!--more-->

# 代码

## 做法一

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
const int mod = 10007;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

inline int add(int x, int y) {
    x += y;
    return x >= mod ? x -= mod : x;
}
inline int sub(int x, int y) {
    x -= y;
    return x < 0 ? x += mod : x;
}
inline int mul(int x, int y) {
    return 1ll * x * y % mod;
}
inline int qpow(int x, ll n) {
    int r = 1;
    while (n > 0) {
        if (n & 1) r = 1ll * r * x % mod;
        n >>= 1; x = 1ll * x * x % mod;
    }
    return r;
}
inline int inv(int x) {
    return qpow(x, mod - 2);
}

int n, k, S[200][200];
vector<int> edge[maxn];

int dp[maxn][200], dp2[maxn][200];
void dfs1(int u, int f) {
    dp[u][0] = 1;
    for (int v: edge[u]) {
        if (v == f) continue;
        dfs1(v, u);
        for (int i = 1; i <= k; i++) {
            dp[u][i] = add(dp[u][i], (dp[v][i] + i * dp[v][i - 1]) % mod);
        }
        dp[u][0] = add(dp[u][0], dp[v][0]);
    }
}
void dfs2(int u, int f) {
    for (int v: edge[u]) {
        if (v == f) continue;
        // for (int i = 1; i <= k; i++) dp2[v][i] = dp[v][i];
        vector<int> tmp(k + 1, 0);
        tmp[0] = sub(dp2[u][0], dp[v][0]);
        for (int i = 1; i <= k; i++) tmp[i] = sub(dp2[u][i], (dp[v][i] + i * dp[v][i - 1]) % mod);
        for (int i = 1; i <= k; i++) dp2[v][i] = add(dp[v][i], (tmp[i] + i * tmp[i - 1]) % mod);
        dp2[v][0] = add(dp[v][0], tmp[0]);
        dfs2(v, u);
    }
}

int main() {
    scanf("%d%d", &n, &k);
    S[1][1] = 1;
    for (int i = 2; i <= k; i++) {
        for (int j = 1; j <= i; j++) {
            S[i][j] = (S[i - 1][j - 1] + j * S[i - 1][j]) % mod;
        }
    }
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    dfs1(1, 0); 
    for (int i = 0; i <= k; i++) dp2[1][i] = dp[1][i];
    dfs2(1, 0);
    for (int i = 1; i <= n; i++) {
        int ans = 0;
        for (int j = 1; j <= k; j++) {
            // dbg(S[k][j], dp2[i][j]);
            ans = add(ans, mul(S[k][j], dp2[i][j]));
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

## 做法二

题目：[多项式](https://nanti.jisuanke.com/t/40551)。

边权不为 $1$。

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

inline int add(int x, int y) {
    x += y;
    return x >= mod ? x -= mod : x;
}
inline int sub(int x, int y) {
    x -= y;
    return x < 0 ? x += mod : x;
}
inline int mul(int x, int y) {
    return 1ll * x * y % mod;
}
inline int qpow(int x, ll n) {
    int r = 1;
    while (n > 0) {
        if (n & 1) r = 1ll * r * x % mod;
        n >>= 1; x = 1ll * x * x % mod;
    }
    return r;
}
inline int inv(int x) {
    return qpow(x, mod - 2);
}

int n, k, ans, f[maxn][20], g[maxn][20], C[20][20];
vector<PII> edge[maxn];

void dfs(int u, int ff) {
    g[u][0] = 1;
    for (auto x: edge[u]) {
        int v = x.first, w = x.second;
        if (v == ff) continue;
        dfs(v, u);
        for (int i = 1; i <= k; i++) {
            int sum = 0;
            for (int j = 0; j <= i; j++) {
                int x = mul(mul(C[i][j], qpow(w, i - j)), g[v][j]);
                sum = add(sum, x);
            }
            g[u][i] = add(g[u][i], sum);

        }
        g[u][0] = add(g[u][0], g[v][0]);
    }
}
void dfs1(int u, int ff) {
    ans = add(ans, f[u][k]);
    for (auto x: edge[u]) {
        int v = x.first, w = x.second;
        if (v == ff) continue;
        vector<int> tmp(k + 1, 0);
        tmp[0] = sub(f[u][0], g[v][0]);
        for (int i = 1; i <= k; i++) {
            int sum = 0;
            for (int j = 0; j <= i; j++) {
                int x = mul(mul(C[i][j], qpow(w, i - j)), g[v][j]);
                sum = add(sum, x);
            }
            tmp[i] = sub(f[u][i], sum);
        }
        f[v][0] = add(tmp[0], g[v][0]);
        for (int i = 1; i <= k; i++) {
            int sum = 0;
            for (int j = 0; j <= i; j++) {
                int x = mul(mul(C[i][j], qpow(w, i - j)), tmp[j]);
                sum = add(sum, x);
            }
            f[v][i] = add(g[v][i], sum);
        }
        dfs1(v, u);
    }
}

int main() {
    C[0][0] = 1;
    for (int i = 1; i < 20; i++) {
        C[i][0] = C[i][i] = 1;
        for (int j = 1; j < i; j++) 
            C[i][j] = add(C[i - 1][j], C[i - 1][j - 1]);
    }
    scanf("%d%d", &n, &k);
    for (int i = 2, u, v, w; i <= n; i++) {
        scanf("%d%d%d", &u, &v, &w);
        edge[u].push_back({v, w});
        edge[v].push_back({u, w});
    }
    dfs(1, 0);
    for (int i = 0; i <= k; i++) f[1][i] = g[1][i];
    dfs1(1, 0);
    int iv = qpow(inv(n), 2);
    printf("%d", mul(iv, ans));
    return 0;
}
```
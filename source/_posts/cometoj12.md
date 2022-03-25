---
mathjax: true
title: Comet OJ Contest 12 F Substring Query 题解
date: 2020-01-25 19:05:04
tags:
- String
- KMP
- Fenwick Tree
- Offline
categories:
- 字符串
- KMP
---

# 题面

定义 `half border` 为长度不超过一半的 `border`。

给定一个长度为 $n$ 的字符串 $s$，$q$ 次询问，每次独立地删除一个子串 $[l_i,r_i]$，左右连起来，询问这个串的 `half border` 长度和。 

其中 $1 \le n, q \le 5 \cdot 10^5$。

# 分析

不妨设左半边比右半边长。

有两部分贡献：

1. `border` 不跨过边界，这部分就是原串长度小于 $\min(l_i, n-r_i+1)$ 的 `border` 之和，预处理后二分即可。

2. `border` 跨过边界了边界，下面分析这部分如何计算。

![coj12-1.png](https://i.loli.net/2020/01/25/jTgrQIwvhizW3cJ.png)

观察到，实际上是前缀的 `border` 位置和后缀的 `border` 位置求了交。

对前缀和后缀分别建立 KMP 的 fail 树。询问离线到前缀树上的每个点。

设有询问 $[1, l_i]$ 和 $[r_i, n]$，在前缀树上根到 $l_i$ 的所有点，若其出现在后缀树上 $r_i$ 的子树内，则其会被算一次长度的贡献。

但是，上面没有考虑到 `half border` 的长度限制，设路径上的一个点 $k$，$2(k+n-r_i+1) \le l_i+n-r_i+1$，也就是 $k$ 小于某个值。对于这个限制，在路径上二分，把询问二次离线到对应的点，使用树状数组容易计算答案。

<!--more-->

用 Lambda 函数迷之 RE？

# 代码

```cpp
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#ifdef XLor
  #define dbg(args...) cout << #args << " -> ", err(args)
  void err() { std::cout<< std::endl; }
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
const int maxn = 500000 + 5;

int n, q;
ll ans[maxn];
char s[maxn];

int fail[maxn];
void getfail(int n, const char s[], int fail[]) {
  fail[1] = 0;
  for (int i = 2; i <= n; i++) {
    int cur = fail[i - 1];
    while (cur && s[cur + 1] != s[i]) cur = fail[cur];
    if (s[cur + 1] == s[i]) cur++;
    fail[i] = cur;
  }
}

struct BIT {
  ll a[maxn];
  void clear() {
    for (int i = 0; i <= n + 1; i++) a[i] = 0;
  }
  void update(int i, int x) {
    for (; i <= n + 1; i += i & -i) a[i] += x;
  }
  ll query(int i) {
    ll r = 0;
    for (; i; i -= i & -i) r += a[i];
    return r;
  }
  ll query(int l, int r) {
    return query(r) - query(l - 1);
  }
} g, h;

struct Solver {
  char s[maxn];
  int pre[maxn], suf[maxn];
  vector<PII> que[maxn], que2[maxn];
  int tin[maxn], tout[maxn];
  vector<int> edge[maxn];
  int tot = 0;
  void dfs(int u) {
    tin[u] = ++tot;
    for (int v: edge[u]) dfs(v);
    tout[u] = tot;
  }
  void build(int n, const char ss[]) {
    for (int i = 0; i <= n + 1; i++) {
      s[i] = ss[i]; que[i].clear(); que2[i].clear();
    }
    getfail(n, s, pre);
    reverse(s + 1, s + 1 + n);
    getfail(n, s, suf);
    reverse(suf + 1, suf + 1 + n);
    for (int i = 1; i <= n; i++) {
      edge[n - suf[i] + 1].push_back(i);
    }
    tot = 0;
    dfs(n + 1);
    for (int i = 0; i <= n + 1; i++) edge[i].clear();
  }

  vector<int> stk;
  void dfs1(int u) {
    stk.push_back(u);
    for (auto& q: que[u]) {
      int v = q.first;
      int lim = (u + v - n - 1) / 2;
      int rk = upper_bound(begin(stk), end(stk), lim) - begin(stk) - 1;
      if (rk >= 0) {
        que2[stk[rk]].push_back(q);
      }
    }
    for (int v: edge[u]) dfs1(v);
    stk.pop_back();
  }
  void dfs2(int u) {
    if (u) {
      h.update(tin[u + 1], u);
      g.update(tin[u + 1], 1);
    }
    for (auto& q: que2[u]) {
      int v = q.first;
      ans[q.second] += h.query(tin[v], tout[v]);
      ans[q.second] += g.query(tin[v], tout[v]) * (n - v + 1);
    }
    for (int v: edge[u]) dfs2(v);
    if (u) {
      h.update(tin[u + 1], -u);
      g.update(tin[u + 1], -1);
    }
  }

  void solve() {
    g.clear(); h.clear();
    for (int i = 1; i <= n; i++) edge[pre[i]].push_back(i);
    stk.clear(); dfs1(0); dfs2(0);
    for (int i = 0; i <= n + 1; i++) edge[i].clear();
  }
} ori, rev;

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d%d%s", &n, &q, s + 1);

    getfail(n, s, fail);
    vector<int> bds; vector<ll> prelen;
    int x = fail[n];
    while (x) bds.push_back(x), x = fail[x];
    reverse(begin(bds), end(bds));
    prelen.assign(bds.size(), 0);
    for (int i = 0; i < (int)bds.size(); i++) {
      prelen[i] = bds[i];
      if (i > 0) prelen[i] += prelen[i - 1];
    }
    auto qSmall = [&](int l) -> ll {
      int rk = upper_bound(begin(bds), end(bds), l) - begin(bds) - 1;
      if (rk < 0) return 0;
      return prelen[rk];
    };
    
    ori.build(n, s);
    reverse(s + 1, s + n + 1);
    rev.build(n, s);
    for (int i = 1, l, r; i <= q; i++) {
      scanf("%d%d", &l, &r);
      if (l - 1 >= n - r) {
        ori.que[l - 1].push_back({ r + 1, i });
      } else {
        rev.que[n - r].push_back({ n - l + 2, i });
      }
      ans[i] = qSmall(min(l - 1, n - r));
    }
    ori.solve(); rev.solve();

    for (int i = 1; i <= q; i++) printf("%lld\n", ans[i]);
  }
  return 0;
}
```
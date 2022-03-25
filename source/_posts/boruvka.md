---
mathjax: true
title: Borůvka 算法
date: 2020-01-26 13:57:28
tags:
- Graph
categories:
- 图论
---

# Borůvka 算法

定义 $E'$ 为我们当前找到的最小生成森林的边。在算法执行过程中，我们逐步向 $E'$ 加边，定义 **连通块** 表示一个点集 $V'\subseteq V$ ，且这个点集中的任意两个点 $u$ , $v$ 在 $E'$ 中的边构成的子图上是连通的（互相可达）。

定义一个连通块的 **最小边** 为它连向其它连通块的边中权值最小的那一条。

初始时， $E'=\varnothing$ ，每个点各自是一个连通块：

1.  计算每个点分别属于哪个连通块。将每个连通块都设为“没有最小边”。
2.  遍历每条边 $(u, v)$ ，如果 $u$ 和 $v$ 不在同一个连通块，就用这条边的边权分别更新 $u$ 和 $v$ 所在连通块的最小边。
3.  如果所有连通块都没有最小边，退出程序，此时的 $E'$ 就是原图最小生成森林的边集。否则，将每个有最小边的连通块的最小边加入 $E'$ ，返回第一步。

当原图连通时，每次迭代连通块数量至少减半，算法只会迭代不超过 $O(\log V)$ 次，而原图不连通时相当于多个子问题，因此算法复杂度是 $O(E\log V)$ 的。给出算法的伪代码：（修改自 [维基百科](https://en.wikipedia.org/wiki/Bor%C5%AFvka%27s_algorithm) ）

$$
\begin{array}{ll}
1 &  \textbf{Input. } \text{A graph }G\text{ whose edges have distinct weights. } \\
2 &  \textbf{Output. } \text{The minimum spanning forest of }G .  \\
3 &  \textbf{Method. }  \\
4 & \text{Initialize a forest }F\text{ to be a set of one-vertex trees} \\
5 &  \textbf{while } \text{True} \\
6 &  \qquad \text{Find the components of }F\text{ and label each vertex of }G\text{ by its component } \\
7 &  \qquad \text{Initialize the cheapest edge for each component to "None"} \\
8 &  \qquad  \textbf{for } \text{each edge }(u, v)\text{ of }G  \\
9 &  \qquad\qquad  \textbf{if }  u\text{ and }v\text{ have different component labels} \\
10 &  \qquad\qquad\qquad  \textbf{if }  (u, v)\text{ is cheaper than the cheapest edge for the component of }u  \\
11 &  \qquad\qquad\qquad\qquad\text{ Set }(u, v)\text{ as the cheapest edge for the component of }u \\
12 &  \qquad\qquad\qquad  \textbf{if }  (u, v)\text{ is cheaper than the cheapest edge for the component of }v  \\
13 &  \qquad\qquad\qquad\qquad\text{ Set }(u, v)\text{ as the cheapest edge for the component of }v  \\
14 &  \qquad  \textbf{if }\text{ all components'cheapest edges are "None"} \\
15 &  \qquad\qquad  \textbf{return }  F \\
16 &  \qquad  \textbf{for }\text{ each component whose cheapest edge is not "None"} \\
17 &  \qquad\qquad\text{ Add its cheapest edge to }F \\
\end{array}
$$

> 转载自 [最小生成树 - OI Wiki](https://oi-wiki.org/graph/mst/#boruvka)

<!--more-->

# [Xor-MST](https://codeforces.com/contest/888/problem/G)

给定一个无向完全图，$i$ 和 $j$ 的边权为 $a_i \oplus a_j$，求最小生成树。

考虑 Borůvka 的过程，最关键的问题是不能枚举边，但是我们只需要知道一个联通块向外的最短边即可，建出字典树查一下。

P.S. 直接按照上述流程实现 Borůvka 算法会超时

## 代码

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
  #define dbg(args...) cout << "\033[32;1m" << #args << " -> ", err(args)
  void err() { std::cout << "\033[39;0m" << std::endl; }
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
const int inf = ~0u >> 1;
const int maxn = 200000 + 5;

namespace Trie {
  const int B = 31;
  int sz = 1, ch[maxn * B][2], val[maxn * B], ed[maxn * B];
  void clear() {
    sz = 1;
  }
  void insert(int x, int i, int v = 1) {
    int u = 1;
    for (int i = B - 1; i >= 0; i--) {
      int c = x >> i & 1;
      if (!ch[u][c]) {
        ch[u][c] = ++sz;
        ms(ch[sz], 0); val[sz] = 0;
      }
      u = ch[u][c];
      val[u] += v;
    }
    if (v > 0) {
      ed[u] = i;
    }
  }
  int qmin(int x) {
    int u = 1;
    for (int i = B - 1; i >= 0; i--) {
      int c = (x >> i & 1);
      if (ch[u][c] && val[ch[u][c]]) {
        u = ch[u][c];
      } else {
        u = ch[u][c ^ 1];
      }
    }
    return ed[u];
  }
}

int n, a[maxn];

int pre[maxn], siz[maxn], last[maxn], nxt[maxn];
int find(int x) {
  return x == pre[x] ? x : pre[x] = find(pre[x]);
}
int join(int x, int y) {
  x = find(x); y = find(y);
  if (x == y) return false;
  if (siz[x] > siz[y]) swap(x, y);
  pre[x] = y; siz[y] += siz[x];
  return true;
}

int main() {
  Trie::clear();
  scanf("%d", &n);
  set<int> st;
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
    // Trie::insert(a[i], i);
    st.insert(a[i]);
    pre[i] = i; siz[i] = 1;
  }
  n = 0;
  for (int x: st) {
    a[++n] = x;
    Trie::insert(x, n);
  }
  ll ans = 0;
  for (int T = 1; T <= 20; T++) {
    for (int i = 1; i <= n; i++) last[i] = 0;
    for (int i = 1; i <= n; i++) {
      nxt[i] = last[find(i)];
      last[find(i)] = i;
    }
    vector<PII> egs;
    for (int i = 1; i <= n; i++) {
      if (i != find(i)) continue;
      int x = last[i];
      while (x) {
        Trie::insert(a[x], 0, -1);
        x = nxt[x];
      }
      x = last[i];
      int mn = inf, u, v;
      while (x) {
        int y = Trie::qmin(a[x]);
        if (y && (a[x] ^ a[y]) < mn) {
          mn = a[x] ^ a[y];
          u = x; v = y;
        }
        x = nxt[x];
      }
      if (mn != inf) {
        egs.push_back({ u, v });
      }
      x = last[i];
      while (x) {
        Trie::insert(a[x], x);
        x = nxt[x];
      }
    }
    if (egs.empty()) break;
    for (auto& e: egs) {
      int x = e.first, y = e.second;
      if (join(x, y)) {
        dbg(x, y, a[x] ^ a[y]);
        ans += a[x] ^ a[y];
      }
    }
  }
  cout << ans << endl;
  return 0;
}
```

# [Connecting Cities](https://atcoder.jp/contests/keyence2019/tasks/keyence2019_e)

给定一个无向完全图，$i$ 和 $j$ 的边权为 $D|i-j|+a_i+a_j$，求最小生成树。

类似上述过程，字典树换成线段树。

## 代码

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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const ll inf = 1ll << 60;
const int maxn = 200000 + 5;

int n, d, a[maxn];

ll F(int i) {
  return -1ll * i * d + a[i];
}
ll G(int i) {
  return 1ll * i * d + a[i];
}

struct SegT {
  #define lson l, m, rt << 1
  #define rson m + 1, r, rt << 1 | 1
  ll mn[maxn << 2]; int pos[maxn << 2];
  void pushup(int rt) {
    if (mn[rt << 1] < mn[rt << 1 | 1]) {
      mn[rt] = mn[rt << 1];
      pos[rt] = pos[rt << 1];
    } else {
      mn[rt] = mn[rt << 1 | 1];
      pos[rt] = pos[rt << 1 | 1];
    }
  }
  void build(function<ll(int)> f, int l = 1, int r = n, int rt = 1) {
    if (l == r) {
      mn[rt] = f(l); pos[rt] = l;
      return ;
    }
    int m = (l + r) / 2;
    build(f, lson); build(f, rson);
    pushup(rt);
  }
  void update(int i, ll x, int l = 1, int r = n, int rt = 1) {
    if (l == r) {
      mn[rt] = x; return ;
    }
    int m = (l + r) / 2;
    if (i <= m) update(i, x, lson);
    else update(i, x, rson);
    pushup(rt);
  }
  pair<ll,int> query(int L, int R, int l = 1, int r = n, int rt = 1) {
    if (L <= l && r <= R) return { mn[rt], pos[rt] };
    int m = (l + r) / 2;
    pair<ll,int> ans(inf, -1);
    if (L <= m) ans = query(L, R, lson);
    if (R > m) ans = min(ans, query(L, R, rson));
    return ans;
  }
} pr, sf;

int pre[maxn], siz[maxn], last[maxn], nxt[maxn];
int find(int x) {
  return x == pre[x] ? x : pre[x] = find(pre[x]);
}
int join(int x, int y) {
  x = find(x); y = find(y);
  if (x == y) return false;
  if (siz[x] > siz[y]) swap(x, y);
  pre[x] = y; siz[y] += siz[x];
  return true;
}

int main() {
  scanf("%d%d", &n, &d);
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
    pre[i] = i; siz[i] = 1;
  }
  pr.build(F); sf.build(G);
  ll ans = 0;
  for (int T = 1; T <= 20; T++) {
    for (int i = 1; i <= n; i++) last[i] = 0;
    for (int i = 1; i <= n; i++) {
      nxt[i] = last[find(i)];
      last[find(i)] = i;
    }
    vector<PII> egs;
    for (int i = 1; i <= n; i++) {
      if (i != find(i)) continue;
      for (int x = last[i]; x; x = nxt[x]) {
        pr.update(x, inf); sf.update(x, inf);
      }
      ll mn = inf; int u, v;
      for (int x = last[i]; x; x = nxt[x]) {
        if (x < n) {
          auto r = sf.query(x + 1, n);
          ll sum = r.first + F(x);
          if (r.second != -1 && sum < mn) {
            mn = sum; u = x; v = r.second;
          }
        }
        if (x > 1) {
          auto r = pr.query(1, x - 1);
          ll sum = r.first + G(x);
          if (r.second != -1 && sum < mn) {
            mn = sum; u = x; v = r.second;
          }
        }
      }
      if (mn < inf) {
        egs.emplace_back(u, v);
      }
      for (int x = last[i]; x; x = nxt[x]) {
        pr.update(x, F(x)); sf.update(x, G(x));
      }
    }
    if (egs.empty()) break;
    for (auto& e: egs) {
      int u = e.first,  v = e.second;
      if (join(u, v)) {
        ans += 1ll * d * abs(u - v) + a[u] + a[v];
      }
    }
  }
  cout << ans << endl;
  return 0;
}
```

# Tree MST

给定一个无向完全图，$i$ 和 $j$ 的边权为 $dis(i,j)+a_i+a_j$，$dis(i,j)$ 表示树上距离，求最小生成树。

考虑 Borůvka 算法的过程，对于一个点集找出向外的最短边合并。

不妨去掉树上一个点，考虑将每棵子树连向外面的最短边，显然是找一个 $dep_u+a_u$ 最小的点向其他所有点连边。如此递归地处理，即类似点分治的过程。

扣出 $n\log n$ 条关建边后，跑一遍 Kruskal 即可。

## 代码

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
  #define dbg(args...) cout << "\033[32;1m" << #args << " -> ", err(args)
  void err() { std::cout << "\033[39;0m" << std::endl; }
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
const ll inf = 1ll << 60;
const int maxn = 200000 + 5;

int n, a[maxn];
vector<PII> edge[maxn];

int vis[maxn], siz[maxn], sum, mn, rt;
void getsz(int u, int f) {
  siz[u] = 1; int mx = 0;
  for (auto& x: edge[u]) {
    int v = x.first;
    if (v == f || vis[v]) continue;
    getsz(v, u);
    siz[u] += siz[v];
    mx = max(mx, siz[v]);
  }
  mx = max(mx, sum - siz[u]);
  if (mx < mn) {
    mn = mx; rt = u;
  }
}
int getsz(int u) {
  sum = siz[u]; mn = 1e9;
  getsz(u, 0); return rt;
}

vector< tuple<ll,int,int> > egs;
void solve(int u) {
  vis[u] = 1;
  ll mn = inf; int y;
  function<void(int,int,ll)> dfs = [&](int u, int f, ll d) {
    if (d + a[u] < mn) {
      mn = d + a[u]; y = u;
    }
    for (auto& x: edge[u]) {
      int v = x.first;
      if (v == f || vis[v]) continue;
      dfs(v, u, d + x.second);
    }
  };
  dfs(u, 0, 0);
  dfs = [&](int u, int f, ll d) {
    egs.emplace_back(mn + d + a[u], u, y);
    for (auto& x: edge[u]) {
      int v = x.first;
      if (v == f || vis[v]) continue;
      dfs(v, u, d + x.second);
    }
  };
  dfs(u, 0, 0);
  for (auto& x: edge[u]) {
    if (vis[x.first]) continue;
    solve(getsz(x.first));
  }
}

int pre[maxn];
int find(int x) {
  return x == pre[x] ? x : pre[x] = find(pre[x]);
}
int join(int x, int y) {
  x = find(x); y = find(y);
  if (x == y) return false;
  pre[x] = y;
  return true;
}

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
  }
  for (int i = 2, u, v, w; i <= n; i++) {
    scanf("%d%d%d", &u, &v, &w);
    edge[u].emplace_back(v, w);
    edge[v].emplace_back(u, w);
  }
  siz[1] = n; solve(getsz(1));
  for (int i = 1; i <= n; i++) pre[i] = i;
  sort(begin(egs), end(egs));
  for (auto& e: egs) {
    ll w; int u, v; tie(w, u, v) = e;
    dbg(w, u, v);
  }
  ll ans = 0;
  for (auto& e: egs) {
    ll w; int u, v; tie(w, u, v) = e;
    if (join(u, v)) {
      ans += w;
    }
  }
  cout << ans << endl;
  return 0;
}
```

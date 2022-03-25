---
mathjax: true
title: Convex Hull Trick
date: 2020-02-14 14:54:52
tags:
- DP
- Geometry
- Data Structure
categories:
- 计算几何
---

## 问题

维护一个一次函数的集合 $\{ f | f(x) = ax+b \}$，对于 $x$ 查询集合内最大的一次函数值，另外还需要支持高效的插入一个一次函数。

实际上就是通常所说的斜率优化 DP，在问题 [1083E - The Fair Nut and Rectangles](https://codeforces.com/contest/1083/problem/E) 中，对矩形排序后有显然的转移方程：

$$
f(i)=x_iy_i - a_i + \max_{0 \le j < i} (-x_j y_i + f(j))
$$

## 做法

把一堆一次函数画出来，实际的最值直线构成一个下凸包（下图加粗部分）。

![凸包](https://codeforces.com/predownloaded/06/b1/06b12912502c14c161d0ec6c21f7d3118652fe53.png)

下凸包由一堆从左到右斜率递增的线段（射线）构成。

在上述问题中，有一些具体的性质，插入的斜率 $-x_i$ 是递减的，询问的横坐标也是递减的。

因此，我们可以从右往左地直线加入凸包，又根据询问的单调性，凸包最后超出当前询问点的部分也是无效的。

我们考虑使用双端队列来维护这个凸包，在枚举当前是第几个矩形时：

询问：左移询问点，删除凸包后面无用的部分，找到最佳位置，计算 dp 值；

![查询](https://codeforces.com/predownloaded/38/c8/38c8c16505aeb550f4c41a5826841aecf3f9e277.png)

插入：向左加入一条新的线段。注意：虽然我们保证了斜率的单调性，但是仍然会出现新加入的直线完全在原有最左边之上的情况。

如下图所示，一个简单的判断方式是看两个直线交点的左右关系。在本文中，如果新加入的直线与凸包上最左边的直线的交点，在原有的最左边的交点的右边，那么说明新加入的直线完全盖住了最左边的直线，因此需要删除。

![插入](https://codeforces.com/predownloaded/5c/5f/5c5fbd97c9aa2ceebe804ac789e5e67837217bdf.png)

## 推广

### 插入斜率有序，询问点任意

仍然按照上述方式维护凸包，询问时通过二分找到该点属于凸包中的哪一段。

### 插入任意，询问任意

动态凸包，一个简单的实现的是使用 `std::multiset`，也可以使用 [李超树](https://cp-algorithms.com/geometry/convex_hull_trick.html)。

多种实现的比较：[比较](https://codeforces.com/blog/entry/63823?#comment-477568)。

> 转载翻译自 [[Tutorial] Convex Hull Trick — Geometry being useful](https://codeforces.com/blog/entry/63823)

<!--more-->

## 1083E - The Fair Nut and Rectangles

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 1000000 + 5;

struct Rect {
  int x, y; ll a;
  bool operator< (const Rect& b) const {
    return x < b.x;
  }
} a[maxn];

int n, que[maxn * 2];
ll dp[maxn];

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d%d%I64d", &a[i].x, &a[i].y, &a[i].a);
  }
  sort(a + 1, a + 1 + n);
  ll ans = 0;
  int ql = maxn, qr = maxn;
  a[0].x = a[0].y = 0; dp[0] = 0;
  auto eval = [&](int i, int y) -> ll {
    return -1ll * a[i].x * y + dp[i];
  };
  auto intersectX = [&](int i, int j) -> long double {
    return (long double)(dp[j] - dp[i]) / (a[j].x - a[i].x);
  };
  // query: desc; add: desc
  for (int i = 1; i <= n; i++) {
    while (qr - ql >= 1 && eval(que[qr], a[i].y) <= eval(que[qr - 1], a[i].y)) {
      qr--;
    }
    dp[i] = 1ll * a[i].x * a[i].y - a[i].a;
    dp[i] += eval(que[qr], a[i].y);
    ans = max(ans, dp[i]);
    while (qr - ql >= 1 && intersectX(i, que[ql]) >= intersectX(que[ql], que[ql + 1])) {
      ql++;
    }
    que[--ql] = i;
  }
  cout << ans << endl;
  return 0;
}
```

## 动态凸包模板

维护一次函数集合 $\{ f | f(x)=ax+b \}$ 的凸包，询问 $\max f(x)$。

```c++
struct LineContainer  {
  static const ll inf = 2e18;
  static bool modeQ;
 
  struct Line {
    mutable ll a, b, end;
    ll intersect(const Line& r) const {
      if (a == r.a) return b > r.b ? inf : -inf;
      ll u = b - r.b, d = r.a - a;
      return u / d - ((u ^ d) < 0 && u % d);
    }
    bool operator<(const Line& r) const {
      return modeQ ? end < r.end : a < r.a;
    }
  };
 
  multiset<Line> S;
  void clear() { S.clear(); }

  // Updates segment end
  bool update(multiset<Line>::iterator it) {
    auto cur = it++; cur->end = inf;
    if (it == S.end()) return false;
    cur->end = cur->intersect(*it);
    return cur->end >= it->end;
  }

  // Insert f(x) = ax + b
  void insert(ll a, ll b) {
    auto it = S.insert({ a, b, inf });
    while (update(it)) it = --S.erase(++it);
    while (it != S.begin() && update(--it)) {
      update(it = --S.erase(++it));
    }
    while (it != S.begin() && update(--it)) {
      update(it = --S.erase(++it));
    }
  }

  // Query max(f(x))
  ll query(ll x) {
    assert(!S.empty());
    modeQ = 1;
    auto l = *S.lower_bound({ 0, 0, x });
    modeQ = 0;
    return l.a * x + l.b;
  }
};

bool LineContainer::modeQ = false;
```

## Sum of Prefix Sums

给定一棵带点权的无根树，定义一条有向路径的权值是它路径上点权的前缀和之和。

显然考虑点分治，问题变成如何合并两条有向路径。定义 $x_i$ 表示长度为 $i$ 的根 $u$ 到点 $x$ 的路径权值是 $ia_i+(i-1)a_{i-1}+\dots+a_1$，$y_i$ 表示相反的路径权值 $a_i+2a_2+\dots+ia_1$。注意重心的处理。

因此，如果我们枚举第二种路径 $y$，令他的本来的路径权值和为 $sumy$，也就是找到 $x_i$，最大化 $x_i+i \cdot sumy + y$，可以发现这就是类似的一次函数集合形式，使用动态凸包容易维护。

### 代码

```c++
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
#include <limits>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 150000 + 5;

struct LineContainer  {
  static const ll inf = 2e18;
  static bool modeQ;
 
  struct Line {
    mutable ll a, b, end;
    ll intersect(const Line& r) const {
      if (a == r.a) return b > r.b ? inf : -inf;
      ll u = b - r.b, d = r.a - a;
      return u / d - ((u ^ d) < 0 && u % d);
    }
    bool operator<(const Line& r) const {
      return modeQ ? end < r.end : a < r.a;
    }
  };
 
  multiset<Line> S;
  void clear() { S.clear(); }

  // Updates segment end
  bool update(multiset<Line>::iterator it) {
    auto cur = it++; cur->end = inf;
    if (it == S.end()) return false;
    cur->end = cur->intersect(*it);
    return cur->end >= it->end;
  }

  // Insert f(x) = ax + b
  void insert(ll a, ll b) {
    auto it = S.insert({ a, b, inf });
    while (update(it)) it = --S.erase(++it);
    while (it != S.begin() && update(--it)) {
      update(it = --S.erase(++it));
    }
    while (it != S.begin() && update(--it)) {
      update(it = --S.erase(++it));
    }
  }

  // Query max(f(x))
  ll query(ll x) {
    assert(!S.empty());
    modeQ = 1;
    auto l = *S.lower_bound({ 0, 0, x });
    modeQ = 0;
    return l.a * x + l.b;
  }
};

bool LineContainer::modeQ = false;

int n, a[maxn];
vector<int> edge[maxn];

int vis[maxn], siz[maxn], sum, mn, rt;
void getrt(int u, int f) {
  siz[u] = 1; int t = 0;
  for (int v: edge[u]) {
    if (v == f || vis[v]) continue;
    getrt(v, u); siz[u] += siz[v];
    t = max(t, siz[v]);
  }
  t = max(t, sum - siz[u]);
  if (t < mn) mn = t, rt = u;
}
int getrt(int u) {
  sum = siz[u]; mn = 1e9; rt = 0;
  getrt(u, 0); return rt;
}

ll ans = 0;
void solve(int u) {
  vis[u] = true;
  LineContainer ch; ch.insert(0, 0);
  function<void(int,int,int,ll,ll)> dfs = [&](int u, int f, int d, ll s1, ll s2) {
    d++; s1 += 1ll * a[u] * d; s2 += a[u];
    ans = max(ans, ch.query(s2) + s1);
    for (int v: edge[u]) {
      if (vis[v] || v == f) continue;
      dfs(v, u, d, s1, s2);
    }
  };
  function<void(int,int,int,ll,ll)> dfs2 = [&](int u, int f, int d, ll s1, ll s2) {
    d++; s2 += a[u]; s1 += s2;
    ch.insert(d, s1);
    for (int v: edge[u]) {
      if (vis[v] || v == f) continue;
      dfs2(v, u, d, s1, s2);
    }
  };
  for (int v: edge[u]) {
    if (vis[v]) continue;
    dfs(v, u, 1, a[u], a[u]);
    dfs2(v, u, 0, 0, 0);
  }
  reverse(begin(edge[u]), end(edge[u]));
  ch.clear(); ch.insert(0, 0);
  for (int v: edge[u]) {
    if (vis[v]) continue;
    dfs(v, u, 1, a[u], a[u]);
    dfs2(v, u, 0, 0, 0);
  }
  reverse(begin(edge[u]), end(edge[u]));
  for (int v: edge[u]) {
    if (vis[v]) continue;
    solve(getrt(v));
  }
}

int main() {
  scanf("%d", &n);
  for (int i = 2, u, v; i <= n; i++) {
    scanf("%d%d", &u, &v);
    edge[u].push_back(v);
    edge[v].push_back(u);
  }
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
  }
  siz[1] = n;
  solve(getrt(1));
  printf("%I64d\n", ans);
  return 0;
}
```

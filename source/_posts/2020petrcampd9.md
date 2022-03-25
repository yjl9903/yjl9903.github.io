---
mathjax: true
title: XXI Open Cup, Grand Prix of Suwon G. Generate The Array 题解
date: 2021-03-27 19:03:06
tags:
  - Solution
  - Trainings
categories:
  - 题解
---

## 题面

有一个长度为 $n$ 的未知数组，第 $i$ 个位置的值有有 $k_i$ 种选择，这个位置选择 $V(i,j)$ 的代价 $C(i,j)$。

当你确定数组后，有 $q$ 次询问 $[l, r]$ 区间内的最大值，这个数组的价值就是所有询问的和减去选数时的代价。

数据范围：$1 \le n \le 300, n \le \sum_{i=1}^n k_i \le 3 \cdot 10^5$。

## 分析

考虑区间 DP，令 $f(l, r)$ 表示确定完区间 $[l, r]$ 的价值。

预处理每个位置 $i$ 被区间 $[l, r]$ 内所有询问覆盖的次数。转移时可以枚举这个区间的某一个位置作为最大值，但是每一个位置有多种选择，这个位置不仅要在价值上比较优，还需要满足比上一层小的限制。

无视上面的区间 DP，我们对于一个点，可以发现这个点产生的贡献关于覆盖次数是一堆一次函数的 $\max$，也就是一个下凸壳。回到上面的 DP，我们在枚举最大值，使用预处理的覆盖次数，可以在凸壳上二分出对应的一次函数，得到最优价值。

其实，我们已经做完了，上面的算法已经能够得到最优解，且最优解一定是合法的。

简单证明一下，我们考虑点 $i$ 和点 $j$，假设点 $i$ 作为大区间时的被覆盖次数为 $x_1$，小区间 $x_2$，点 $j$ 类似地为 $x_3$ 和 $x_4$，显然 $x_1 > x_2, x_3 > x_4$。不妨设两个点 $i < j$ 分别只有两条线段 $f(x) = k_i x - b_i$ 和 $f(x) = k_j x - b_j$，且 $k_i > k_j$。因此，$i$ 必须作为大区间的最大值，而 $j$ 需要在内层。

合法解时的价值为 $k_i x_1 - b_i + k_j x_4 - b_j$，非法解时的价值为 $k_i x_2 - b_i + k_j x_3 - b_j$。

注意到 $x_1 - x_2 = x_3 - x_4$，因为这个差是所有区间左端点在 $i$ 左侧和右端点在 $j$ 右侧。

因此 $k_i (x_1 - x_2) > k_j (x_3 - x_4)$。

<!--more-->

## 代码

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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const ll inf = 2e18;
const int maxn = 300 + 5;

struct LineContainer  {
  static bool modeQ; // Toggles operator < mode

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

  // Insert f(x) = ax + b, time: O(log n)
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

  // Query max(f(x)), time: O(log n)
  ll query(ll x) {
    assert(!S.empty());
    modeQ = 1;
    auto l = *S.lower_bound({ 0, 0, x });
    modeQ = 0;
    return l.a * x + l.b;
  }
} f[maxn];

bool LineContainer::modeQ = false;

int n, Q[maxn][maxn], sQ[maxn][maxn];

int qcnt(int i, int l, int r) {
  assert(l <= i && i <= r);
  return sQ[i][r] + sQ[l - 1][i - 1] - sQ[i][i - 1] - sQ[l - 1][r];
}

ll g[maxn][maxn];
ll dfs(int l, int r) {
  if (l > r) return 0ll;
  if (g[l][r] != -inf) return g[l][r];
  ll ans = -inf;
  for (int i = l; i <= r; i++) {
    ll cur = dfs(l, i - 1) + dfs(i + 1, r);
    cur += f[i].query(qcnt(i, l, r));
    ans = max(ans, cur);
  }
  return g[l][r] = ans;
}

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    int sum = 0;
    for (int j = 0; j < i; j++) {
      sQ[i][j] = sQ[i - 1][j];
    }
    for (int j = i; j <= n; j++) {
      scanf("%d", &Q[i][j]);
      sum += Q[i][j];
      sQ[i][j] = sum + sQ[i - 1][j];
    }
  }
  for (int i = 1; i <= n; i++) {
    int k;
    scanf("%d", &k);
    for (int j = 0; j < k; j++) {
      ll v, b;
      scanf("%lld%lld", &v, &b);
      f[i].insert(v, -b);
    }
  }
  for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= n; j++) {
      g[i][j] = -inf;
    }
  }
  printf("%lld\n", dfs(1, n));
  return 0;
}
```

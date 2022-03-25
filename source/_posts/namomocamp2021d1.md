---
mathjax: true
title: 2 个轮廓线 DP 问题
date: 2021-01-25 19:59:02
tags:
- DP
categories:
- 训练
---

# 2019 年 CCPC 秦皇岛 G Game on Chessboard

## 题面

给定一个 $12 \times 12$ 的正方形棋盘，放了黑白两种棋子，每个格子有权值 $w(i,j)$。有两种操作，删除一个棋子 $(i,j)$，花费为 $w(i,j)$，删除一对黑白棋子 $(i_1,j_1)$ 和 $(i_2,j_2)$，并且满足这两个棋子的左下角范围内不能有其它棋子，花费为 $|w(i_1,j_1)-w(i_2,j_2)|$。求删除所有棋子的最小花费。

## 分析

注意到，每次能够删除的棋子在一条从坐上到右下的轮廓线上，对轮廓线进行状压，$0$ 表示向下，$1$ 表示向右。

轮廓线有 $2n \choose n$ 条，状压枚举时，找到拐角点进行转移。

时间复杂度 $O(n^2 {2n \choose n})$。

# 2020 年 ICPC 上海 F Fountains

## 题面

给定一个长度为 $9$ 的序列 $a_1, a_2, \dots, a_n$，求选出 $k=1,2,\dots,\frac{n(n+1)}{2}$ 个不同的子区间时，最大化 $\sum_{L \le R} \max_{i=1}^k [L \le l_i \le r_i \le R] w(l_i, r_i)$，其中 $w(l,r)=\sum_{i=l}^r a_i$。

## 分析

首先，我们将线段 $[l,r]$ 看成平面上的一个点 $(l,r)$，显然这个点左上角的点对应线段都包含线段 $[l,r]$。

对所有子区间线段按权值由大到小排序，考虑 $dp(i,j,S)$ 表示考虑到了前 $i$ 条线段，选出了 $j$ 个，状态 $S$ 内的所有区间都已经确定了包含的线段，显然 $S$ 是一个从左下角到右上角的轮廓线。当我们枚举下一个放进来的线段时，就和原有状态求一个并，因为排序的缘故，只需要更新没有更新过的点的答案，同时维护出新的轮廓线。

时间复杂度 $O((\frac{n(n+1)}{2})^2 {2n \choose n})$。

<!--more-->

# 代码

## Game on Chessboard

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
#include <queue>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 20 + 5;

int getBit(int x, int b) {
  return x >> b & 1;
}

int n, a[maxn][maxn], col[maxn][maxn];
char s[maxn];

int dis[(1 << 24) + 5];

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; i++) {
    scanf("%s", s);
    for (int j = 0; j < n; j++) {
      if (s[j] == 'W') {
        col[i][j] = 1;
      } else if (s[j] == 'B') {
        col[i][j] = 2;
      }
    }
  }
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
      scanf("%d", &a[i][j]);
    }
  }
  ms(dis, -1);
  int st = ((1 << n) - 1) << n, ed = (1 << n) - 1;
  dis[st] = 0;
  vector<int> corner;
  vector<int> ca, cc;
  for (int u = st; u >= ed; u--) {
    if (__builtin_popcount(u) != n) continue;
    if (dis[u] == -1) continue;
    int x = 0, y = 0;
    corner.clear();
    ca.clear();
    cc.clear();
    for (int i = 0; i + 1 < n + n; i++) {
      if (getBit(u, i) == 0 && getBit(u, i + 1) == 1) {
        if (col[x][y]) {
          corner.emplace_back(i);
          ca.push_back(a[x][y]);
          cc.push_back(col[x][y]);
        }
        int nxt = u ^ (1 << i) ^ (1 << (i + 1));
        int val = dis[u];
        if (col[x][y]) {
          val += a[x][y];
        }
        if (dis[nxt] == -1 || dis[nxt] > val) {
          dis[nxt] = val;
        }
      }
      if (getBit(u, i) == 0) {
        x++;
      } else {
        y++;
      }
    }
    for (int i = 0; i < corner.size(); i++) {
      for (int j = 0; j < i; j++) {
        if (cc[i] != cc[j]) {
          int nxt = u;
          nxt ^= (1 << corner[i]) ^ (1 << (corner[i] + 1));
          nxt ^= (1 << corner[j]) ^ (1 << (corner[j] + 1));
          int val = dis[u] + abs(ca[i] - ca[j]);
          if (dis[nxt] == -1 || dis[nxt] > val) {
            dis[nxt] = val;
          }
        }
      }
    }
  }
  printf("%d\n", dis[ed]);
  return 0;
}
```

## Fountains

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
#include <unordered_map>
#include <bitset>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100 + 5;

int n, a[maxn];
ll pre[maxn];
unordered_map<bitset<maxn>,ll> f[maxn], g[maxn];

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
    pre[i] = pre[i - 1] + a[i];
  }
  ll sum = 0;
  vector<PII> line;
  for (int i = 1; i <= n; i++) {
    for (int j = i; j <= n; j++) {
      sum += pre[j] - pre[i - 1];
      line.emplace_back(i, j);
    }
  }
  sort(line.begin(), line.end(), [&](const PII& lhs, const PII& rhs) {
    ll a = pre[lhs.second] - pre[lhs.first - 1];
    ll b = pre[rhs.second] - pre[rhs.first - 1];
    return a > b;
  });
  f[0][0] = 0;
  for (int i = 0; i < line.size(); i++, swap(f, g)) {
    for (int j = 0; j <= i + 1; j++) {
      g[j].clear();
    }
    ll cur = pre[line[i].second] - pre[line[i].first - 1];
    for (int j = 0; j <= i; j++) {
      for (auto& x: f[j]) {
        auto state = x.first;
        ll val = x.second;
        g[j][state] = max(g[j][state], val);
        for (int l = 1; l <= line[i].first; l++) {
          for (int r = line[i].second; r <= n; r++) {
            if (!state[n * (l - 1) + (r - 1)]) {
              val += cur;
              state[n * (l - 1) + (r - 1)] = 1;
            }
          }
        }
        g[j + 1][state] = max(g[j + 1][state], val);
      }
    }
  }
  for (int i = 1; i <= n * (n + 1) / 2; i++) {
    ll val = 0;
    for (auto& x: f[i]) {
      val = max(val, x.second);
    }
    printf("%lld\n", sum - val);
  }
  return 0;
}
```

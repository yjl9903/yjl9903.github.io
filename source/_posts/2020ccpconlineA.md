---
mathjax: true
title: 2020 CCPC 网络选拔赛 A Art Class
date: 2020-12-21 01:00:30
tags:
- SegTree
categories:
- 数据结构
- 线段树
---

# 题面

依次将 $n$ 个下边界在 $x$ 轴上的矩形涂黑，求每次涂色后总图形的周长。

其中 $1 \le n \le 2 \times 10^5$。

# 分析

横线部分就是线段覆盖，这是平凡的。

竖线部分，更新操作就是区间取 $\max$，对于线段树节点 $[l,r]$，需要维护 $\sum_{i=l}^{r-1} |a_{i}-a_{i+1}|$。

考虑 Segment Tree Beats，我们在线段树节点维护了区间最小值和区间次小值。当最大值更新为 $x$，$x$ 小于节点次大值，将区间内所有最小值提升为 $x$，可以发现这个过程中改变的是最小值和相邻高点之间的差。因此，我们对于一个线段树节点区间，维护所有相邻的是否是最小值导致间断点，假设我们维护了这样的间断点个数，那么对于区间答案的容易进行更新。

间断点的个数容易维护，首先它可能从左右子树上传得到，然后在考虑将左右区间拼接点的贡献即可。

最后，注意我们区间覆盖最小值时打的标记，也包含了懒标记效果，记得下传信息。注意维护次小值的细节。

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
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 400000 + 5;

int n, nn, L[maxn], R[maxn], H[maxn];
vector<int> lsh;

namespace SegTC {
  int cnt[maxn << 2], len[maxn << 2];
  void upd(int rt, int l, int r) {
    if (cnt[rt]) {
      len[rt] = lsh[r] - lsh[l - 1];
    } else if (l < r) {
      len[rt] = len[rt << 1] + len[rt << 1 | 1];
    } else {
      len[rt] = 0;
    }
  }
  void build(int l = 1, int r = nn, int rt = 1) {
    cnt[rt] = len[rt] = 0;
    if (l == r) return ;
    int m = (l + r) / 2;
    build(lson);
    build(rson);
  }
  void update(int L, int R, int l = 1, int r = nn, int rt = 1) {
    if (L <= l && r <= R) {
      cnt[rt]++;
      upd(rt, l, r);
      return ;
    }
    int m = (l + r) / 2;
    if (L <= m) update(L, R, lson);
    if (R > m) update(L, R, rson);
    upd(rt, l, r);
  }

  int ans() {
    return len[1];
  }
}

namespace SegTB {
  ll a[maxn << 2];
  int al[maxn << 2], ar[maxn << 2], cnt[maxn << 2];
  int mn[maxn << 2], smn[maxn << 2];

  void pushup(int rt) {
    int ls = rt << 1, rs = rt << 1 | 1;

    al[rt] = al[ls];
    ar[rt] = ar[rs];
    if (mn[ls] == mn[rs]) {
      mn[rt] = mn[ls];
      smn[rt] = min(smn[ls], smn[rs]);
    } else if (mn[ls] < mn[rs]) {
      mn[rt] = mn[ls];
      smn[rt] = min(smn[ls], mn[rs]);
    } else if (mn[ls] > mn[rs]) {
      mn[rt] = mn[rs];
      smn[rt] = min(smn[rs], mn[ls]);
    } else {
      assert(false);
    }

    cnt[rt] = 0;
    if (mn[rt] == mn[ls]) {
      cnt[rt] += cnt[ls];
    }
    if (mn[rt] == mn[rs]) {
      cnt[rt] += cnt[rs];
    }
    if (ar[ls] == mn[rt] && al[rs] != mn[rt]) {
      cnt[rt]++;
    } else if (ar[ls] != mn[rt] && al[rs] == mn[rt]) {
      cnt[rt]++;
    }
    a[rt] = a[ls] + a[rs] + abs(ar[ls] - al[rs]);
  }

  void build(int l = 1, int r = nn, int rt = 1) {
    if (l == r) {
      a[rt] = 0;
      cnt[rt] = 0;
      al[rt] = ar[rt] = 0;
      mn[rt] = 0;
      smn[rt] = inf;
      return ;
    }
    int m = (l + r) / 2;
    build(lson);
    build(rson);
    pushup(rt);
  }

  void upd(int rt, int x) {
    al[rt] = max(al[rt], x);
    ar[rt] = max(ar[rt], x);
    a[rt] -= 1ll * (x - mn[rt]) * cnt[rt];
    mn[rt] = x;
  }
  void update(int L, int R, int x, int l = 1, int r = nn, int rt = 1) {
    if (L <= l && r <= R) {
      if (x <= mn[rt]) return ;
      if (x < smn[rt]) {
        upd(rt, x);
        return ;
      }
      assert(l != r);
    }
    int m = (l + r) / 2;
    {
      int ls = rt << 1, rs = rt << 1 | 1;
      if (mn[ls] < mn[rt]) {
        upd(ls, mn[rt]);
      }
      if (mn[rs] < mn[rt]) {
        upd(rs, mn[rt]);
      }
    }
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    pushup(rt);
  }

  ll ans() {
    return a[1];
  }
}

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d", &n);
    lsh.clear();
    lsh.push_back(0);
    lsh.push_back(inf);
    for (int i = 1; i <= n; i++) {
      scanf("%d%d%d", L + i, R + i, H + i);
      lsh.push_back(L[i]);
      lsh.push_back(R[i]);
    }
    sort(lsh.begin(), lsh.end());
    lsh.resize(unique(lsh.begin(), lsh.end()) - lsh.begin());
    nn = lsh.size();
    auto gid = [&](int x) {
      return lower_bound(lsh.begin(), lsh.end(), x) - lsh.begin() + 1;
    };
    SegTC::build();
    SegTB::build();
    for (int i = 1; i <= n; i++) {
      int l = gid(L[i]);
      int r = gid(R[i]) - 1;
      assert(l <= r);
      SegTC::update(l, r);
      SegTB::update(l, r, H[i]);
      printf("%lld\n", SegTB::ans() + 2ll * SegTC::ans());
    }
  }
  return 0;
}
```

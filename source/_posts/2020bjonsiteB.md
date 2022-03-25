---
mathjax: true
title: 2020 ICPC 小米邀请赛决赛 B Rikka with Maximum Segment Sum 题解
date: 2020-12-10 22:35:24
tags:
- DP
- Divide and Conquer
- Solution
categories:
- 题解
---

## 题面

给定一个长度为 $n$ 的数组，对于所有子数组求最大子段和。

其中 $2 \le n \le 10^5$。

## 分析

考虑分治，也就是需要统计跨越中点 $m$ 的贡献。

注意到，跨越中点的区间的最大子段和位置，它要么完全在左半边，要么完全在右半边，要么是中点往左右延申得到的。

我们在区间 $[l,m]$ 枚举左端点的位置，可以发现右端点从左往右移动的过程中：首先是一段使用完全在左半边，然后是一段左右拼接，最后是完全在右半边。

二分出 $2$ 个边界的位置即可。

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
using namespace std;
using ll = long long;
const ll inf = 1ll << 60;
const int maxn = 200000 + 5;

int n, a[maxn];
ll suf1[maxn], suf2[maxn], pre1[maxn], pre2[maxn];
ll pre3[maxn], pre4[maxn];

ll solve(int l, int r) {
  if (l == r) {
    return a[l];
  }
  if (l > r) {
    return 0;
  }
  int m = (l + r) / 2;
  ll ans = 0;
  {
    ll sum = 0, dp = 0;
    suf1[m + 1] = -inf;
    suf2[m + 1] = -inf;
    for (int i = m; i >= l; i--) {
      sum += a[i];
      dp = max(dp, 0ll) + a[i];
      suf1[i] = max(suf1[i + 1], sum);
      suf2[i] = max(suf2[i + 1], dp);
    }
  }
  {
    ll sum = 0, dp = 0;
    pre1[m] = -inf;
    pre2[m] = -inf;
    pre3[m] = 0;
    pre4[m] = 0;
    for (int i = m + 1; i <= r; i++) {
      sum += a[i];
      dp = max(dp, 0ll) + a[i];
      pre1[i] = max(pre1[i - 1], sum);
      pre2[i] = max(pre2[i - 1], dp);
      pre3[i] = pre1[i] + pre3[i - 1];
      pre4[i] = pre2[i] + pre4[i - 1];
    }
  }
  for (int i = m; i >= l; i--) {
    int p1 = upper_bound(pre1 + m + 1, pre1 + r + 1, suf2[i] - suf1[i]) - pre1;
    int p2 = upper_bound(pre2 + m + 1, pre2 + r + 1, suf2[i]) - pre2;

    int pos1 = min(p1, p2);
    ans += 1ll * (pos1 - m) * suf2[i];
    // suf2 => [m, pos1)

    int ql = pos1, qr = r, pos2 = pos1 - 1;
    while (ql <= qr) {
      int m = (ql + qr) / 2;
      if (pre1[m] + suf1[i] >= pre2[m]) {
        pos2 = m;
        ql = m + 1;
      } else {
        qr = m - 1;
      }
    }
    if (pos1 <= pos2) {
      ans += 1ll * (pos2 - pos1 + 1) * suf1[i] + pre3[pos2] - pre3[pos1 - 1];
    }
    // suf1 + pre1 => [pos1, pos2]

    if (pos2 < r) {
      ans += pre4[r] - pre4[pos2];
      // pre2 => (pos2, r]
    }
  }
  ans += solve(l, m - 1);
  ans += solve(m + 1, r);
  return ans;
}

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
  }
  printf("%llu\n", solve(1, n));
  return 0;
}
```

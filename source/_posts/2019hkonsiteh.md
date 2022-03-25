---
mathjax: true
title: 2019 ICPC 香港现场赛 H. Hold the Line 题解
date: 2019-12-12 00:56:53
tags:
  - Data Structure
  - DP
  - Solution
categories:
  - 数据结构
  - 线段树
---

# 题意

有一排长度为 $n$ 空白序列，有 $q$ 个时刻，每个时刻会将序列中的某一个位置填上数字 $h_i$，或者询问序列 $[l,r]$ 区间中与 $h$ 差的绝对值的最小值。

其中 $1 \le n \le 5 \cdot 10^5, 1 \le q \le 10^6$。

# 分析

有一个显然的做法，转化为四维偏序问题，时间复杂度 $O(q\log^3q)$，显然 T 飞了。

将询问离线，从左到右枚举右端点 $r$，如果位置 $r$ 曾经出现，则将 $r$ 插入数据结构，枚举这个点结束的询问进行回答。

令位置 $i$ 的出现时间为 $appear(i)$，对于询问 $(t,l,r,h)$，其中 $l,r,h$ 为输入，$t$ 为时间戳，我们需要在数据结构中查询权值小于等于 $h$ 的最大位置 $pos$，满足 $pos \ge l$ 且 $appear(pos) < t$，或者权值大于等于 $h$ 的最小位置，满足同样的条件。

为此，我们维护一棵权值线段树。

记权值线段树上的结点 $u$ 对应区间 $[l,r]$，在 $u$ 上维护出权值在 $[l,r]$ 范围内的所有位置。

查询时，如果 $[l,r]$ 包含于询问的值域区间，则检查当前区间内是否存在一个位置满足上述条件，如果满足则继续往下递归，直到叶子，否则剪枝。显然，如果一个区间不含合法点，会直接被剪枝，因此实际经过的结点仍然只有 $\log n$ 个。

但是，上述做法时间仍然难以接受，因为每个结点内需要使用一个数据结构进行维护，时间复杂度为 $O((n+q)\log^2n)$。

继续对其进行优化，我们注意到一个事实，在一个线段树结点内，如果 $i < j$ 且 $appear(i)>appear(j)$，那么 $i$ 一定不如 $j$ 优，因为 $j$ 更靠近右端点，出现时间也更加早。

因为我们可以直接线段树结点上，维护一个关于出现时间递增的单调栈，因为最多只有 $n\log n$ 个结点，因此插入的时间复杂度是 $O(n \log n)$。查询时，在该单调栈上二分一个位置 $pos$，使得 $pos \ge l$ 且 $appear(pos) < t$。

最终，时间复杂度为 $O(n\log q + q \log^2q)$。

<!--more-->

# 代码

```c++
#include <iostream>
#include <cstdio>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 500000 + 5;
const int maxm = 1000000 + 5;

int n, nn, q, ans[maxm];
int ap[maxn], a[maxn];

vector<int> lsh;
int gid(int x) {
  return lower_bound(begin(lsh), end(lsh), x) - begin(lsh) + 1;
}

struct Query {
  int op, x, y, h;
} all[maxm];

vector<tuple<int,int,int> > que[maxn];
vector<int> stks[maxm * 4];
void build(int l = 1, int r = nn, int rt = 1) {
  stks[rt].clear();
  if (l == r) return ;
  int m = (l + r) / 2;
  build(lson); build(rson);
}
void insert(int x) {
  int ti = ap[x], val = a[x];
  if (ti > q) return ;
  function<void(int,int,int)> dfs = [&](int l, int r, int rt) {
    auto& stk = stks[rt];
    while (!stk.empty() && ap[stk.back()] >= ti) stk.pop_back();
    stk.push_back(x); // stack with appear time up
    if (l == r) return ;
    int m = (l + r) / 2;
    if (val <= m) dfs(lson);
    else dfs(rson);
  };
  dfs(1, nn, 1);
}
int query(int L, int id, int h) {
  // check whether appear time < id, and location >= L
  auto check = [&](const vector<int>& stk) -> bool {
    int l = 0, r = (int)stk.size() - 1;
    while (l <= r) {
      int m = (l + r) / 2;
      if (stk[m] >= L && ap[stk[m]] < id) return true;
      if (stk[m] < L) l = m + 1;
      else r = m - 1;
    }
    return false;
  };
  int ok = 0;
  function<void(int,int,int)> qmx = [&](int l, int r, int rt) {
    if (ok || stks[rt].empty()) return ;
    if (r <= h) {
      if (!check(stks[rt])) return ;
      if (l == r) {
        ok = l; return ;
      }
    }
    int m = (l + r) / 2;
    if (h > m) qmx(rson);
    qmx(lson);
  };
  function<void(int,int,int)> qmn = [&](int l, int r, int rt) {
    if (ok || stks[rt].empty()) return ;
    if (l >= h) {
      if (!check(stks[rt])) return ;
      if (l == r) {
        ok = l; return ;
      }
    }
    int m = (l + r) / 2;
    if (h <= m) qmn(lson);
    qmn(rson);
  };
  int ans = inf;
  qmx(1, nn, 1);
  if (ok) ans = lsh[h - 1] - lsh[ok - 1];
  ok = 0; qmn(1, nn, 1);
  if (ok) ans = min(ans, lsh[ok - 1] - lsh[h - 1]);
  return ans < inf ? ans : -1;
}

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d%d", &n, &q);
    lsh.clear();
    for (int i = 0; i <= n; i++) {
      que[i].clear(); ap[i] = q + 1;
    }
    for (int i = 1; i <= q; i++) {
      scanf("%d", &all[i].op);
      if (all[i].op == 0) {
        scanf("%d%d", &all[i].x, &all[i].h);
      } else if (all[i].op == 1) {
        scanf("%d%d%d", &all[i].x, &all[i].y, &all[i].h);
      }
      lsh.push_back(all[i].h);
    }
    sort(begin(lsh), end(lsh));
    lsh.resize(unique(begin(lsh), end(lsh)) - begin(lsh));
    nn = (int)lsh.size(); build();
    for (int i = 1; i <= q; i++) {
      if (all[i].op == 0) {
        ap[all[i].x] = i; // i-th pos appear time
        a[all[i].x] = gid(all[i].h); // value on i-th pos
      } else if (all[i].op == 1) {
        que[all[i].y].emplace_back(all[i].x, gid(all[i].h), i);
      }
    }
    for (int i = 1; i <= n; i++) {
      insert(i);
      for (auto& q: que[i]) {
        int l, h, id; tie(l, h, id) = q;
        ans[id] = query(l, id, h);
      }
    }
    for (int i = 1; i <= q; i++) {
      if (!all[i].op) continue;
      printf("%d\n", ans[i]);
    }
  }  
  return 0;
}
```
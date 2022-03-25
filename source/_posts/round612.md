---
mathjax: true
title: Codeforces Round 612 题解
tags:
  - Codeforces
  - String
  - Data Structure
categories:
  - Codeforces
date: 2020-01-06 21:50:43
---

# E Fedya the Potter Strikes Back

强制在线，维护一个串和一个数组，每次向后加一个字母和一个值，定义一个子串的权值为数组对应区间的最小值，求出所有和串的子串，满足等于相应长度的前缀条件，价值之和。

显然，只需要算出每次 `push back` 产生的贡献即可。

首先，可以动态维护一下每个前缀的 `border` 集合，遍历一遍得到答案，但是 `border` 集合的总大小可能是 $O(n^2)$ 的。因此，我们可以考虑维护 `border` 集合的变化，显然这部分是均摊 $O(n)$ 的（参考 KMP 算法证明）。

考虑 KMP 的过程，从上一点的 `fail`，暴力往上跳找到第一个点，其对应前缀的下一个字母等于当前字母，显然上跳的中间过程，这些前一次的 `border` 无法扩展，需要从 `border` 集合中被删除。

但是，这部分是不够的。还需要考虑，当前的最长 `border` 之前的转移情况，实际上等同于这个 `border` 作为前缀时的转移情况，将其丢进被删除集合即可。

官方题解这里给出了另外一个做法，多维护一个上跳指针，表示往上第一个后继字母与当前不同的点，每次暴力往上跳即可（次数等价于变化大小）。

最后，考虑如何维护出答案的增量。加入新的 `border`，删除 `border`，`border` 集合保留未删除的部分，相当于每个权值对于新加入元素对于 $w$ 取 $\min$。使用 `std::map` 维护每种取值的个数，取 $\min$ 就暴力枚举比 $w$ 大的元素。这样复杂度是均摊 $O(n \log n)$ 的，因为如果我们对同一个 `std::map` 取 $\min$，其大小（种类数）不会增加，而操作代价实质是大小的变化量。

<!--more-->

# 代码

## E

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
typedef long long ll;
typedef unsigned long long ull;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 600000 + 5;

int q, n, s[maxn], fail[maxn];
vector<int> del[maxn];

namespace SegT {
  #define lson l, m, rt << 1
  #define rson m + 1, r, rt << 1 | 1
  int mn[maxn * 4];
  void build(int l = 1, int r = q, int rt = 1) {
    if (l == r) {
      mn[rt] = inf; return ;
    }
    int m = (l + r) / 2;
    build(lson); build(rson);
  }
  void update(int pos, int val, int l = 1, int r = q, int rt = 1) {
    if (l == r) {
      mn[rt] = val; return ;
    }
    int m = (l + r) / 2;
    if (pos <= m) update(pos, val, lson);
    else update(pos, val, rson);
    mn[rt] = min(mn[rt << 1], mn[rt << 1 | 1]);
  }
  int query(int L, int R, int l = 1, int r = q, int rt = 1) {
    if (L <= l && r <= R) return mn[rt];
    int m = (l + r) / 2, ans = inf;
    if (L <= m) ans = query(L, R, lson);
    if (R > m) ans = min(ans, query(L, R, rson));
    return ans;
  }
}

ull sum;
map<int,int> mp;
void push(int x) {
  sum += x; mp[x]++;
}
void pop(int x) {
  sum -= x; mp[x]--;
}
void updateMin(int x) {
  for (auto it = mp.upper_bound(x); it != mp.end(); it++) {
    mp[x] += it->second;
    sum += 1ll * (x - it->first) * it->second;
  }
  while (!mp.empty() && mp.rbegin()->first > x) {
    auto it = mp.end();
    mp.erase(--it);
  }
}

int main() {
  scanf("%d", &q);
  SegT::build();
  ull ans = 0;
  for (n = 1; n <= q; n++) {
    char c[2]; int w;
    scanf("%s%d", c, &w);
    s[n] = (c[0] - 'a' + ans) % 26;
    w ^= (ans & ((1 << 30) - 1));

    SegT::update(n, w);
    updateMin(w);

    if (n == 1) {
      push(w); ans += sum;
      printf("%I64u\n", ans);
      continue;
    }

    int cur = fail[n - 1];
    while (cur && s[cur + 1] != s[n]) {
      del[n].push_back(cur + 1); // length
      cur = fail[cur];
    }
    if (s[cur + 1] == s[n]) cur++;
    fail[n] = cur;
    for (int x: del[cur]) del[n].push_back(x);

    if (s[1] == s[n]) push(w);

    for (int x: del[n]) pop(SegT::query(n - x + 1, n));
    
    printf("%I64u\n", ans += sum);
  }
  return 0;
}
```

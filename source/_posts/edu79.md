---
mathjax: true
title: Educational Round 79 题解
tags:
  - Codeforces
  - Solution
  - Math
  - DP
categories:
  - Codeforces
  - Educational Round
date: 2019-12-28 17:04:49
---

# A New Year Garland

# B Verse For Santa

读错题了。

# C Stack of Presents

# D Santa's Bot

# E New Year Permutations

定义一个长度为 $n$ 的排列合法，当且仅当，满足：排列可以划分成一系列连续的子区间，每个子区间是一个周期等于子区间长度的置换群，且子区间的第一个位置是该子区间的最大值。求长度为 $n$ 的字典序第 $k$ 大的合法排列。

首先，dp 出每个点开头有多个合法排列，即枚举这个点开始的子区间长度，长度为 $n$ 的合法置换群有 $(n-2)!$ 种。

然后，开始逐位确定，枚举当前位置应该填多少，等价于子区间的结束位置，直到方案数大于 $k$，说明子区间在这里结束。

这里需要生成字典序第 $k$ 的一个置换群，这里的 $k$ 等于全局的 $k$ 除掉后面的方案数（每种子区间排列对应后面的排列），全局的 $k$ 取模。

最后，问题就是解决生成这样的置换群，还是考虑逐位确定，使用并查集检查防止提前出现环，方案数使用 $(n-2)!$ 进行更新。

注意一些细节：

+ 使用 `C++` 防止爆 $long long$，自定义一个带上限的加法和乘法；

+ 注意 `break`；

+ 字典序编号为 $0$ 到总方案数减一。

# F New Year and Handle Change

wqs 二分。

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
const ll inf = 2e18 + 1;
const int maxn = 100000 + 5;

ll mul(ll x, ll y) {
  if (!x || !y) return 0;
  if (inf / x < y) return inf;
  return x * y;
}

int n;
ll k, fac[100], dp[100];

int main() {
  fac[0] = fac[1] = 1;
  for (int i = 2; i < 100; i++) fac[i] = mul(i - 1, fac[i - 1]);
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d%I64d", &n, &k);
    dp[n + 1] = 1;
    for (int i = n; i >= 1; i--) {
      dp[i] = 0;
      for (int j = i; j <= n; j++) {
        dp[i] += mul(fac[j - i], dp[j + 1]);
        dp[i] = min(dp[i], inf);
      }
    }
    k--;
    if (k >= dp[1]) {
      puts("-1"); continue;
    }
    vector<int> ans;
    auto gen = [&](int len, ll k, int shift) {
      vector<int> fa(len);
      for (int i = 0; i < len; i++) fa[i] = i;
      function<int(int)> find = [&](int x) {
        return x == fa[x] ? x : fa[x] = find(fa[x]);
      };
      function<void(int,int)> join = [&](int x, int y) {
        fa[find(x)] = find(y);
      };
      set<int> st = { len - 1 };
      ans.push_back(len - 1 + shift);
      join(0, len - 1);
      int cnt = len - 2;
      for (int i = 1; i < len; i++) {
        for (int j = 0; j + 1 < len; j++) {
          if (st.count(j)) continue;
          if (i == len - 1) {
            ans.push_back(j + shift);
            break;
          }
          if (find(i) == find(j)) continue;
          if (k >= (cnt >= 0 ? fac[cnt] : 1)) {
            k -= (cnt >= 0 ? fac[cnt] : 1);
          } else {
            cnt--;
            ans.push_back(j + shift);
            st.insert(j); join(i, j);
            break;
          }
        }
      }
    };
    for (int i = 1; i <= n; ) {
      for (int j = i; j <= n; j++) {
        ll val = mul(fac[j - i], dp[j + 1]);
        if (val <= k) {
          k -= val;
        } else {
          gen(j - i + 1, k / dp[j + 1], i);
          k %= dp[j + 1]; i = j + 1;
          break;
        }
      }
    }
    for (int x: ans) printf("%d ", x);
    puts("");
  }
  return 0;
}
```

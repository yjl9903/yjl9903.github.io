---
mathjax: true
title: 2020 ICPC 小米邀请赛决赛 D Rikka with New Year's Party 题解
date: 2020-12-11 16:56:58
tags:
- String
- Suffix Array
categories:
- 字符串
---

# 题面

给定一个长度为 $10^5$ 的字符串，求同构意义下的本质不同子串数。

# 分析

考虑如何求一个串的本质不同子串数，实际上对他求一个后缀数组和 `height` 数组即可，进一步转化我们只要能对求出两个后缀的 LCP 长度，就能实现后缀排序。

将同构转化为距离上一次出现该字符的距离，可以发现每个后缀和整体的差别仅有 $26$ 个开始位置。

我们现在考虑如何求两个后缀的 LCP，首先两个字符的情况可以参考 [2020 牛客暑期多校训练营第 1 场 A](https://xlor.cn/2020/7/2020nowcoder1/)，我们将其扩展到更大字符集。

![aaababccabdb](https://i.loli.net/2020/12/11/SsMzPi4BdHcjeqh.png)

我们将这个串，用每种字符的第一次出现位置分割开来。如果两个串同构，那么这些位置必然也是一一对应相同的。

求 LCP 时，只需要一段一段考虑即可。

<!--more-->

# 代码

```c++
#include <cstdio>
#include <cassert>
#include <cstring>
#include <algorithm>
#include <utility>
#include <vector>
using namespace std;
using ll = long long;
const int maxn = 100000 + 5;

namespace SA {
  const int L = 18;

  int n, m, sa[maxn], h[maxn], c[maxn], x[maxn], y[maxn];
  int dp[L][maxn];
  
  void rsort() {
    for (int i = 1; i <= m; i++) c[i] = 0;
    for (int i = 1; i <= n; i++) c[x[i]]++;
    for (int i = 1; i <= m; i++) c[i] += c[i - 1];
    for (int i = n; i >= 1; i--) sa[c[x[y[i]]]--] = y[i];
  }
  int cmp(int i, int j, int k) {
    int a = i + k > n ? -1 : y[i + k];
    int b = j + k > n ? -1 : y[j + k];
    return y[i] == y[j] && a == b;
  }
  void build(int nn, int* s) {
    n = nn; m = nn + 1;
    for (int i = 1; i <= n; i++) x[i] = s[i], y[i] = i;
    rsort();
    for (int k = 1, p; k <= n; k += k, m = p) {
      p = 0;
      for (int i = n; i > n - k; i--) y[++p] = i;
      for (int i = 1; i <= n; i++) if (sa[i] > k) y[++p] = sa[i] - k;
      rsort();
      for (int i = 1; i <= n; i++) swap(x[i], y[i]);
      x[sa[1]] = p = 1;
      for (int i = 2; i <= n; i++) x[sa[i]] = cmp(sa[i], sa[i - 1], k) ? p : ++p;
    }
    for (int i = 1; i <= n; i++) x[sa[i]] = i;
    for (int i = 1, k = 0; i <= n; i++) {
      if (k) k--;
      int j = sa[x[i] - 1];
      while (i + k <= n && j + k <= n && s[i + k] == s[j + k]) k++;
      h[x[i]] = k;
    }
    for (int i = 1; i <= n; i++) dp[0][i] = h[i];
    for (int j = 1; j < L; j++) {
      for (int i = 1; i + (1 << j) <= n + 1; i++) {
        dp[j][i] = min(dp[j - 1][i], dp[j - 1][i + (1 << (j - 1))]);
      }
    }
  }
  int qlcp(int l, int r) {
    if (l <= 0 || l > n) return 0;
    if (r <= 0 || r > n) return 0;
    if (l == r) return n - l + 1;
    l = x[l]; r = x[r];
    if (l > r) swap(l, r);
    l++; int k = __lg(r - l + 1);
    return min(dp[k][l], dp[k][r - (1 << k) + 1]);
  }
}

int n, a[maxn], ord[maxn];
int ch[maxn][26], nxt[maxn][26];
char s[maxn];

int lcp(int x, int y) {
  int Bx = x, By = y;
  int len = 0;
  x--; y--;
  for (int j = 0; j < 26 && x + 1 <= n && y + 1 <= n; j++) {
    int l = SA::qlcp(x + 1, y + 1);
    if (x + 1 + l < nxt[Bx][j] && y + 1 + l < nxt[By][j]) {
      return len + l;
    }
    if (nxt[Bx][j] - Bx == nxt[By][j] - By) {
      len += nxt[Bx][j] - x;
      x = nxt[Bx][j];
      y = nxt[By][j];
    } else if (nxt[Bx][j] - Bx < nxt[By][j] - By) {
      return len + nxt[Bx][j] - x - 1;
    } else {
      return len + nxt[By][j] - y - 1;
    }
  }
  return len + SA::qlcp(x + 1, y + 1);
}

int main() {
  scanf("%d%s", &n, s + 1);
  vector<int> last(26, -1);
  for (int i = 1; i <= n; i++) {
    ord[i] = i;
    int ch = s[i] - 'a';
    if (last[ch] == -1) {
      a[i] = n + 1;
    } else {
      a[i] = i - last[ch];
    }
    last[ch] = i;
  }
  SA::build(n, a);
  for (int j = 0; j < 26; j++) {
    nxt[n + 1][j] = n + n + n;
    ch[n + 1][j] = n + n + n;
  }
  for (int i = n; i >= 1; i--) {
    memcpy(nxt[i], nxt[i + 1], sizeof nxt[i + 1]);
    nxt[i][s[i] - 'a'] = i;
  }
  for (int i = n; i >= 1; i--) {
    memcpy(ch[i], nxt[i], sizeof nxt[i]);
    sort(nxt[i], nxt[i] + 26);
  }
  sort(ord + 1, ord + 1 + n, [&](int x, int y) {
    int len = lcp(x, y);
    if (x + len > n || y + len > n) {
      return x + len > n;
    }
    int chX = a[x + len], chY = a[y + len];
    for (int j = 0; j < 26; j++) {
      if (ch[x][j] == x + len) {
        chX = 0;
        break;
      }
    }
    for (int j = 0; j < 26; j++) {
      if (ch[y][j] == y + len) {
        chY = 0;
        break;
      }
    }
    return chX < chY;
  });
  ll ans = 1ll * n * (n + 1) / 2;
  for (int i = 1; i < n; i++) {
    ans -= lcp(ord[i], ord[i + 1]);
  }
  printf("%lld\n", ans);
  return 0;
}
```
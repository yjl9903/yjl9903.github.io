---
mathjax: true
title: Trie 图上的随机游走
date: 2020-02-06 22:48:20
tags:
- String
- Math
categories:
- 字符串
---

## 问题描述

给定 $n$ 个长度均为 $m$ 的字符串，现在你开始投骰子，有 $P_c$ 的概率投出字符 $c$，求第一次出现的串是第 $i$ 个的概率。

## 一般做法

对这个字符串集合建出 Trie 图后，问题等价于给定一些终点的无向图随机游走问题。

令结点数为 $s$，建出概率转移矩阵后，实际上只有 $s-1$ 条有效的方程，因为无法转移到根节点。但是，随机游走必定会在某个时刻终结，也就是走到所有终止结点的概率和为 $1$。添加这么一条方程后，高斯消元，即可解出在每个终止结点结束的概率。

时间复杂度：$O(nm|\Sigma|+n^3m^3)$。

## 加强做法

令概率生成函数 $[x^k]G(x)$ 表示在 $k$ 时刻尚未停止的概率，$[x^k]F_i(x)$ 表示在 $k$ 时刻末尾是第 $i$ 个串的概率。

我们要求的就是 $F_i(1),F_i(2),\dots, F_i(n)$。

考虑在每个尚未停止的时刻，往后走一步的情况，即 $xG(x)$，再加上初始时的概率 $1$，这时要么在此处迎来某个串的结束，要么尚未停止，有下式。

$$
1+xG(x)=G(x)+\sum_{i=1}^n F_i(x)
$$

记 $P(S)$ 表示一个串的出现概率，即 $P(S)=\prod_{i=1}^{|S|} P_{S_i}$

对于每个串 $i$，在一个未终止时刻，往后加第 $i$ 个串，即 $G(x)P(s_i)x^m$。这时情况很多，因为加这个串的时候可能中途出现已经终止的情况，设第 $j$ 个串在新加串的 $k$ 位置终止，因此 $s_i[1 \dots k]=s_j[ m - k + 1 \dots m]$，即第 $i$ 个串的前缀等于长度为 $k$ 的第 $j$ 个串的后缀，此时的概率生成函数就是 $F_j(x)P(s_i[k+1 \dots m])x^{m-k}$。

我们枚举每个串 $j$ 和它的前缀长度 $k$，有下式：

$$
G(x)P(s_i)x^m = \sum_{j=1}^n \sum_{k=1}^m [ s_i[1 \dots k]=s_j[ m - k + 1 \dots m] ] F_j(x)P(s_i[k+1 \dots m])x^{m-k}
$$

结合我们要求的，带入 $x=1$，可以得到 $n$ 个方程，但是 $G(1)$ 也是未知量，因此有 $n+1$ 个未知量，联立 $\sum_{i=1} F_i(1)=1$，即可得到 $n+1$ 个方程，高斯消元。注意这里我们的变量数是 $n+1$，不是一般做法的最大 $nm$。

时间复杂度：$O(n^2m+n^3)$。

<!--more-->

## 代码

### BZOJ 1444

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
#include <queue>
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
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 1000 + 5;

const double eps = 1e-5;

int n, l, S, fin[maxn];
double p[20];
char s[maxn];

namespace acam {
  static const int maxp = 100000 + 5;
  static const int S = 26;
  static const int Base = 'A';
  int sz, ch[maxp][10], fail[maxp], val[maxp];;
  int node() {
    ms(ch[++sz], 0);
    fail[sz] = val[sz] = 0;
    return sz;
  }
  void clear() {
    sz = 0; node();
    for (int i = 0; i < S; i++) ch[0][i] = 1;
  }
  int insert(char* s, int i) {
    int u = 1;
    for (int i = 0; s[i]; i++) {
      int v = s[i] - Base; // !
      if (!ch[u][v]) ch[u][v] = node();
      u = ch[u][v];
    }
    val[u]++;
    return u;
  }
  void build() {
    queue<int> q; q.push(1);
    while (!q.empty()) {
      int t = q.front(); q.pop();
      for (int i = 0; i < S; i++) {
        if (ch[t][i]) {
          fail[ch[t][i]] = ch[fail[t]][i];
          q.push(ch[t][i]);
        } else {
          ch[t][i] = ch[fail[t]][i];
        }
      }
    }
  }
}
using namespace acam;

double a[maxn][maxn], ans[maxn];
bool solve(int n) {
  for (int i = 1; i <= n; i++) {
    int r = i;
    for (int j = i + 1; j <= n; j++)
      if (abs(a[j][i]) > abs(a[r][i])) r = j;
    if (abs(a[r][i]) < eps) return false;
    swap(a[r], a[i]);
    double inv = a[i][i];
    for (int j = i; j <= n + 1; j++) a[i][j] /= inv;
    for (int j = i + 1; j <= n; j++) {
      double inv = a[j][i];
      for (int k = i; k <= n + 1; k++)
        a[j][k] -= inv * a[i][k];
    }
  }
  for (int i = n; i >= 1; i--) {
    ans[i] = a[i][n + 1];
    for (int j = i + 1; j <= n; j++)
      ans[i] -= a[i][j] * ans[j];
  }
  return true;
}

int main() {
  scanf("%d%d%d", &n, &l, &S);
  acam::clear();
  for (int i = 0, a, b; i < S; i++) {
    scanf("%d%d", &a, &b);
    p[i] = double(a) / b;
  }
  for (int i = 1; i <= n; i++) {
    scanf("%s", s);
    fin[i] = acam::insert(s, i);
  }
  acam::build();
  for (int i = 1; i <= sz; i++) {
    a[i][i] = -1;
  }
  for (int i = 1; i <= sz; i++) {
    if (val[i]) continue;
    for (int j = 0; j < S; j++) {
      a[ch[i][j]][i] += p[j];
    }
  }
  a[1][sz + 1] = 1;
  for (int i = 1; i <= sz; i++) {
    if (val[i]) {
      a[1][i] = 1;
    } else {
      a[1][i] = 0;
    }
  }
  bool x = solve(sz);

  for (int i = 1; i <= n; i++) {
    double r = ans[fin[i]];
    if (r > 0) {
      printf("%.2lf\n", r);
    } else {
      puts("0.00");
    }
  }
  return 0;
}
```

### 「SDOI2017」硬币游戏

注意：eps 的精度。

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
#include <queue>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 500 + 5;
const long double eps = 1e-100;

int n, m, fin[maxn];
char s[maxn];
long double two[maxn], a[maxn][maxn], ans[maxn];

namespace acam {
  static const int maxp = 100000 + 5;
  static const int S = 2;
  int sz, ch[maxp][S], fail[maxp], len[maxp];
  vector<int> nds[maxp];
  int node() {
    ms(ch[++sz], 0);
    fail[sz] = len[sz] = 0;
    return sz;
  }
  void clear() {
    sz = 0; node();
    for (int i = 0; i < S; i++) ch[0][i] = 1;
  }
  int insert(char* s, int p) {
    int u = 1;
    for (int i = 0; s[i]; i++) {
      int v = s[i] == 'H'; // !
      if (!ch[u][v]) ch[u][v] = node();
      u = ch[u][v];
      len[u] = i + 1;
      nds[u].push_back(p);
    }
    return u;
  }
  void build() {
    queue<int> q; q.push(1);
    while (!q.empty()) {
      int t = q.front(); q.pop();
      for (int i = 0; i < S; i++) {
        if (ch[t][i]) {
          fail[ch[t][i]] = ch[fail[t]][i];
          q.push(ch[t][i]);
        } else {
          ch[t][i] = ch[fail[t]][i];
        }
      }
    }
    for (int i = 1; i <= n; i++) {
      a[i][n + 1] = -1.0;
      int x = fin[i];
      while (x > 1) {
        for (int u: nds[x]) {
          a[u][i] += two[len[x]];
        }
        x = fail[x];
      }
    }
    a[n + 1][n + 2] = 1;
    for (int i = 1; i <= n; i++) {
      a[n + 1][i] = 1;
    }
  }
}

bool solve(int n) {
  for (int i = 1; i <= n; i++) {
    int r = i;
    for (int j = i + 1; j <= n; j++)
      if (abs(a[j][i]) > abs(a[r][i])) r = j;
    if (abs(a[r][i]) < eps) return false;
    swap(a[r], a[i]);
    double inv = a[i][i];
    for (int j = i; j <= n + 1; j++) a[i][j] /= inv;
    for (int j = i + 1; j <= n; j++) {
      double inv = a[j][i];
      for (int k = i; k <= n + 1; k++)
        a[j][k] -= inv * a[i][k];
    }
  }
  for (int i = n; i >= 1; i--) {
    ans[i] = a[i][n + 1];
    for (int j = i + 1; j <= n; j++)
      ans[i] -= a[i][j] * ans[j];
  }
  return true;
}

int main() {
  acam::clear();
  scanf("%d%d", &n, &m);
  two[0] = 1.0;
  for (int i = 1; i <= m; i++) {
    two[i] = two[i - 1] * 2.0;
  }
  for (int i = 1; i <= n; i++) {
    scanf("%s", s);
    fin[i] = acam::insert(s, i);
  }
  acam::build();
  bool f = solve(n + 1);
  for (int i = 1; i <= n; i++) {
    printf("%.6Lf\n", ans[i]);
  }
  return 0;
}
```

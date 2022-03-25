---
mathjax: true
title: 2020 ICPC 小米邀请赛决赛 M Rikka with Employees 题解
date: 2020-11-22 19:45:24
tags:
- Tree
- Solution
categories:
- Tree
---

## 题面

给定一棵 $n$ 个点的有根树，初始时，每个点处于上班状态，你现在需要构造一个操作序列，使得每个点都满足一次，一个点满足当且仅当只有这个点的子树在上班，其他全在下班。

操作序列会维护一个栈结构，分为 $3$ 种操作：

1. 点 $u$ 下班，加入栈中；

2. 栈顶 $u$ 弹栈，$u$ 重新上班；

3. 报告点 $u$ 处于满足状态。

其中 $2 \le n \le 10^5$，操作序列长度不超过 $9 \times 10^6$。

## 分析

对于一棵大小为 $n$ 的树，我们可以选择一条链（不妨选择重链），不断将外部的点删掉，可以用 $n$ 次操作，由浅到深将整条链都满足一次。之后，这条链就不在有用了，并且把树划分为一个森林，有多个等价的子问题。

思考一些暴力的做法，留下一棵树，其他入栈，对这棵树递归构造完成后（递归后满足子树所有点在栈中），需要去弹出下一个子问题的树，因为是栈结构，所以需要弹出栈顶的两棵树，再将做好的树入栈，以此类推。显然，这样一开始做的树就会被反复加入和弹出，不够优。

不妨使用哈夫曼编码以子树的大小作为频率值进行合并，在构造好的哈夫曼树上递归构造即可。由于哈夫曼编码的性质，每个点深度频率乘积的和，大约为 $O(n \log n)$ 级别，可以通过本题。

<!--more-->

## 代码

```c++
#include <cstdio>
#include <functional>
#include <utility>
#include <vector>
#include <queue>
using namespace std;
using PII = pair<int,int>;
const int maxn = 200000 + 5;

int n, siz[maxn], son[maxn];
int tot, L[maxn], R[maxn], rdfn[maxn];
vector<int> edge[maxn];

void getsz(int u) {
  L[u] = ++tot;
  rdfn[tot] = u;
  siz[u] = 1;
  for (int v: edge[u]) {
    getsz(v);
    siz[u] += siz[v];
    if (siz[v] > siz[son[u]]) {
      son[u] = v;
    }
  }
  R[u] = tot;
}

int push(int u) {
  putchar('+');
  printf("%d", u);
  return 1;
}
int pushTree(int u) {
  for (int i = L[u]; i <= R[u]; i++) {
    push(rdfn[i]);
  }
  return R[u] - L[u] + 1;
}
void pop(int len = 1) {
  while (len--) {
    putchar('-');
  }
}
void ok(int u) {
  putchar('=');
  printf("%d", u);
}

void solve(int u) {
  int sz = 0;
  for (int x = u; x; x = son[x]) {
    ok(x);
    sz += push(x);
    for (int v: edge[x]) {
      if (v == son[x]) continue;
      sz += pushTree(v);
    }
  }
  priority_queue<PII, vector<PII>, greater<PII> > pq;
  for (int x = u; x; x = son[x]) {
    for (int v: edge[x]) {
      if (v == son[x]) continue;
      pq.emplace(siz[v], v);
    }
  }
  if (pq.empty()) return ;
  pop(sz);
  vector<PII> ch;
  while (pq.size() >= 2) {
    auto x = pq.top(); pq.pop();
    auto y = pq.top(); pq.pop();
    if (x.first > y.first) swap(x, y);
    ch.emplace_back(x.second, y.second);
    pq.emplace(x.first + y.first, ch.size() + n);
  }
  function<int(int)> add = [&](int u) {
    if (u <= n) {
      return pushTree(u);
    } else {
      u -= n + 1;
      return add(ch[u].first) + add(ch[u].second);
    }
  };
  function<int(int)> dfs = [&](int u) {
    if (u <= n) {
      solve(u);
      return siz[u];
    } else {
      u -= n + 1;
      int sz = add(ch[u].second);
      sz += dfs(ch[u].first);
      pop(sz);
      add(ch[u].first);
      dfs(ch[u].second);
      return sz;
    }
  };
  for (int x = u; x; x = son[x]) {
    push(x);
  }
  dfs(pq.top().second);
}

int main() {
  scanf("%d", &n);
  for (int i = 2; i <= n; i++) {
    int fa;
    scanf("%d", &fa);
    edge[fa].push_back(i);
  }
  getsz(1);
  solve(1);
  puts("!");
  return 0;
}
```

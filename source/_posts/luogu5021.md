---
mathjax: true
title: NOIp2018 赛道修建
date: 2019-01-08 18:34:17
tags:
- Tree
- Binary Search
categories:
- 树
---

# 题面

给一棵 $n$ 个点带权边权的无根树，要求在在树上选一些边，构成树上的正好 $m$ 条路径，并且每条边最多只能在一条路径中出现，要求最大化每个选择方案中的最小路径长度。

# 分析

二分答案。

## 链

二分答案，$check(mid)$ 判断能不能将一条链划分成 $m$ 个最小值为 $mid$ 的段，每段可以贪心的选取大于等于 $mid$ 的最小段。

## 菊花

只有两种情况，一种是只选择一条边，第二种是选择跨过根节点的两条边。

对于长度大于等于 $mid$ 的边，单独构成一条链，对于长度小于 $mid$ 的边，贪心地去和最小的能连在一起长度大于等于 $mid$ 的边组成一条链。

## 回到一般的树上

树上每个节点与其父亲只有一条边，因此在一个节点完成类似于菊花情况的匹配的时候，可能会剩下多条边，而这些边中选一个最大的传递到父节点，与当前节点到父节点的边组成一条链，在父亲节点类似的做菊花情况的操作。

<!--more-->

# 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <deque>
#include <set>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int mod = 1e9 + 7;
const int maxn = 50000 + 5;

vector<PII> edge[maxn];
int n, m, tag, cnt;

int dfs(int u, int f) {
    multiset<int> st;
    for (auto& v: edge[u]) {
        if (v.first == f) continue;
        int x = dfs(v.first, u) + v.second;
        if (x >= tag) cnt++;
        else st.insert(x);
    }
    int mx = 0;
    while (!st.empty()) {
        int tot = *st.begin(); st.erase(st.begin()); 
        auto it = st.lower_bound(tag - tot);
        if (it == st.end()) mx = max(mx, tot);
        else st.erase(it), cnt++;
    }
    return mx;
}
int check(int x) {
    tag = x; cnt = 0;
    dfs(1, 0);
    return cnt >= m;
}

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 2, u, v, l; i <= n; i++) {
        scanf("%d%d%d", &u, &v, &l);
        edge[u].emplace_back(v, l);
        edge[v].emplace_back(u, l);
    }
    int l = 1, r = 1e9, ans = -1;
    while (l <= r) {
        int m = (l + r) / 2;
        if (check(m)) l = m + 1, ans = m;
        else r = m - 1;
    }
    printf("%d\n", ans);
    return 0;
}
```
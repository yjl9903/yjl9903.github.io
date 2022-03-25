---
mathjax: true
title: 线段树优化建图
date: 2019-05-09 15:15:40
tags:
- SegTree
- Graph
categories:
- 图论
---

# 模板

线段树优化建图 + 拓扑排序。

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <queue>
#include <functional>
#include <utility>
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int mod = 998244353;
const int maxn = 500000 + 5;

vector<int> edge[maxn];
int n, b[maxn], a[maxn], pos[maxn], id[maxn], deg[maxn];

void build(int l, int r, int rt) {
    pos[rt] = -1;
    if (l == r) {
        id[l] = rt; pos[rt] = l;
        return ;
    }
    int m = (l + r) >> 1;
    edge[rt << 1].push_back(rt);
    edge[rt << 1 | 1].push_back(rt);
    deg[rt] += 2;
    build(lson); build(rson);
}
void link(int L, int R, int p, int l, int r, int rt) {
    if (L <= l && r <= R) {
        edge[rt].push_back(p); 
        deg[p]++;
        return ;
    }
    int m = (l + r) >> 1;
    if (L <= m) link(L, R, p, lson);
    if (R > m) link(L, R, p, rson);
}

int main(){
    scanf("%d", &n); 
    for (int i = 0; i < n; i++) scanf("%d", b + i);
    build(0, n - 1, 1);
    for (int i = 0; i < n; i++) {
        if (b[i] % n == i) continue;
        int s = b[i] % n;
        if (s < i) link(s, i - 1, id[i], 0, n - 1, 1);
        else {
            link(s, n - 1, id[i], 0, n - 1, 1);
            if (i) link(0, i - 1, id[i], 0, n - 1, 1);
        }
    }
    int last = 0;
    priority_queue<PII,vector<PII>,greater<PII> > pq;
    for (int i = 0; i < n; i++) {
        if (deg[id[i]] == 0) pq.push({b[i], id[i]});
    }
    while (!pq.empty()) {
        PII t = pq.top(); pq.pop();
        if (t.first != -1) a[last++] = t.first;
        for (int& v: edge[t.second]) {
            deg[v]--;
            if (deg[v] == 0) {
                if (pos[v] == -1) pq.push({-1, v});
                else pq.push({b[pos[v]], v});
            }
        }
    }
    for (int i = 0; i < n; i++) printf("%d%c", a[i], " \n"[i == n - 1]);
    return 0;
}
```
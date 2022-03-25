---
mathjax: true
title: 并查集
date: 2019-03-28 15:43:09
tags:
- Data Structure
categories:
- 数据结构
---

# 模板

```c++
int pre[maxn];
void init() {
    for (int i = 1; i <= n; i++) pre[i] = i;
}
int find(int x) {
    return x == pre[x] ? x : pre[x] = find(pre[x]);
}
void join(int x, int y) {
    x = find(x); y = find(y);
    if (x == y) return ;
    pre[x] = y;
}
```

<!--more-->

# 可持久化并查集

注意不要漏掉某些根节点的传递！

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define lson l, m, ls[rt]
#define rson m + 1, r, rs[rt]
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 200000 + 5;

int n, m, root[maxn];

int tot, ls[maxn * 40], rs[maxn * 40], pre[maxn * 40], siz[maxn * 40];
void build(int l, int r, int& rt) {
    rt = ++tot;
    if (l == r) {
        pre[rt] = l; return ;
    }
    int m = (l + r) >> 1;
    build(lson); build(rson);
}
void update(int i, int x, int last, int l, int r, int& rt) {
    rt = ++tot; ls[rt] = ls[last]; rs[rt] = rs[last];
    if (l == r) {
        pre[rt] = x; siz[rt] = siz[last]; return ;
    }
    int m = (l + r) >> 1;
    if (i <= m) update(i, x, ls[last], lson);
    else update(i, x, rs[last], rson);
}
void add(int i, int l, int r, int& rt) {
    if (l == r) {
        siz[rt]++; return ;
    }
    int m = (l + r) >> 1;
    if (i <= m) add(i, lson);
    else add(i, rson);
}
int query(int i, int l, int r, int rt) {
    if (l == r) return rt;
    int m = (l + r) >> 1;
    if (i <= m) return query(i, lson);
    else return query(i, rson);
}

int find(int i, int x) {
    int p = query(x, 1, n, root[i]);
    return x == pre[p] ? p : find(i, pre[p]);
}
void join(int i, int x, int y) {
    x = find(i - 1, x); y = find(i - 1, y);
    if (pre[x] == pre[y]) return ;
    if (siz[x] > siz[y]) swap(x, y);
    update(pre[x], pre[y], root[i - 1], 1, n, root[i]);
    if (siz[x] == siz[y]) add(pre[y], 1, n, root[i]);
}

int main() {
    scanf("%d%d", &n, &m);
    build(1, n, root[0]);
    for (int i = 1, op, a, b; i <= m; i++) {
        scanf("%d%d", &op, &a);
        if (op == 1) {
            root[i] = root[i - 1]; // important!
            scanf("%d", &b);
            join(i, a, b);
        } else if (op == 2) {
            root[i] = root[a];
        } else if (op == 3) {
            scanf("%d", &b);
            root[i] = root[i - 1];
            a = find(i, a); b = find(i, b);
            if (pre[a] == pre[b]) puts("1");
            else puts("0");
        }
    }
    return 0;
}
```

# 带撤销并查集

```c++
int pre[maxn << 1], siz[maxn << 1], xcnt[maxn << 1], ycnt[maxn << 1];
int find(int x) {
    while (x != pre[x]) x = pre[x]; return x;
}
ll sum = 0;
vector<PII> sta;
bool join(int x, int y) {
    // dbg(x, y);
    x = find(x); y = find(y);
    if (x == y) return 0;
    sum -= 1ll * xcnt[x] * ycnt[x]; sum -= 1ll * xcnt[y] * ycnt[y];
    if (siz[x] > siz[y]) swap(x, y);
    pre[x] = y; siz[y] += siz[x];
    xcnt[y] += xcnt[x]; ycnt[y] += ycnt[x];
    sum += 1ll * xcnt[y] * ycnt[y];
    sta.push_back({x, y});
    return 1;
}
void undo() {
    PII tp = sta.back(); sta.pop_back();
    int x = tp.first, y = tp.second;
    pre[x] = x; siz[y] -= siz[x];
    sum -= 1ll * xcnt[y] * ycnt[y];
    xcnt[y] -= xcnt[x]; ycnt[y] -= ycnt[x];
    sum += 1ll * xcnt[x] * ycnt[x]; sum += 1ll * xcnt[y] * ycnt[y];
}
```
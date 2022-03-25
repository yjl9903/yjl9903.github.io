---
mathjax: true
title: 配对堆
date: 2018-12-27 01:29:59
tags:
- Data Structure
- Heap
categories:
- 数据结构
- 堆
---

# 实现

儿子兄弟表示法，维护一个多叉树。

1. `merge(rootX, rootY)` : 将两个树根按照堆的要求连边即可，返回新的树根。

2. `pop(root)` : 将根节点的所有儿子拿出来从左到右一个接一个合并。

# 复杂度

+ 最值查询 $O(1)$

+ 删除最值 $O(\log n)$

+ 插入 $O(1)$

+ 合并 $O(1)$

<!--more-->

# 洛谷2713 罗马游戏

每个点都有一个权值，给两个操作：

1. 将两个点合并；

2. 将一个点所属的块内的最小值点删除，输出最小值。

维护配对堆的森林。

## 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000000 + 5;

namespace pairHeap {
    struct node {
        int val, id, head, nxt, fa;
        node() { head = nxt = fa = 0; }
    } a[maxn];
    int rt[maxn];
    inline int merge(int u, int v) {
        if (!u || !v) return u + v;
        if (a[u].val > a[v].val) swap(u, v);
        a[v].nxt = a[u].head; a[u].head = v; a[v].fa = u;
        return u;
    }
    inline int pop(int x) {
        queue<int> q;
        int tp = 0;
        for (int i = a[x].head; i; i = a[i].nxt) {
            // if (x == a[i].fa) {  
            //     q.push(i); a[i].fa = 0;
            // }
            q.push(i); a[i].fa = 0;
        }
        while (q.size() > 1) {
            int x = q.front(); q.pop(); 
            int y = q.front(); q.pop();
            q.push(merge(x, y));
        }
        if (q.size()) return q.front();
        else return 0;
    }
}
using namespace pairHeap;

int n, q, vis[maxn], pre[maxn];

int find(int x) { return x == pre[x] ? x : pre[x] = find(pre[x]); }

int main(){
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &a[i].val), a[i].id = i, pre[i] = rt[i] = i;
    scanf("%d", &q); char op[2]; int x, y;
    while (q--) {
        scanf("%s%d", op, &x);
        if (op[0] == 'M') {
            scanf("%d", &y);
            if (vis[x] || vis[y]) continue;
            x = find(x); y = find(y);
            if (x == y) continue;
            pre[y] = x;
            rt[x] = merge(rt[x], rt[y]);
        }
        if (op[0] == 'K') {
            if (vis[x]) {
                puts("0"); continue;
            }
            x = find(x);
            printf("%d\n", a[rt[x]].val);
            vis[a[rt[x]].id] = 1;
            rt[x] = pop(rt[x]);
        }
    }
    return 0;
}
```
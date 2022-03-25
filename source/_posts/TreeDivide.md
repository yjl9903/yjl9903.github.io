---
mathjax: true
title: 树分治
date: 2019-02-13 15:44:27
tags:
- Tree
categories:
- 树
---

点分治可以处理一类静态树上路径信息统计的问题。

点分治，选择一个点作为根，处理过这个根的路径，在将树分成若干子树递归的处理。

和序列的分治一样，我们希望树尽量平分，使得递归层数尽可能的小。

贪心的解决这个问题，选一个最大子树最小的点，即树的重心。这样递归的层数是 $O(\log n)$。

<!--more-->

# SPOJ Free Tour II

询问树上路径经过不超过 $k$ 个黑点的最长路径。

考虑点分治，每次处理所有过根节点的路径。

假设我们已经处理完了 $u$ 的前 $m$ 个子树，我们需要用第 $m+1$ 棵子树的信息更新答案，并合并这两个集合的信息。

对于第 $m+1$ 棵子树，我们需要子树内所有点到根路径上的黑点数 $cnt$ 和路径长度 $dis$，询问时只需要在前 $m$ 棵子树的信息内查询黑点数小于 $k-cnt+black[u]$ 的最大路径长度即可，这个可以使用线段树（树状数组）维护。

时间复杂度：$O(n \log^2 n)$。

使用启发式合并可以做到 $O(n \log n)$。

## 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int mod = 998244353;
const int inf = 2147483647; 
const int maxn = 200000 + 50;

int tr[maxn];
inline int lowbit(int x) { return x & -x; }
inline void update(int i, int x) {
    while (i < maxn) tr[i] = max(tr[i], x), i += lowbit(i);
}
inline void del(int i) {
    while (i < maxn) tr[i] = 0, i += lowbit(i);
}
inline int query(int i) {
    int r = 0; while (i > 0) r = max(tr[i], r), i -= lowbit(i);
    return r;
}

struct edge {
    int to, nxt, d;
} g[maxn << 1];
int head[maxn], tot;
void add(int u, int v, int w) {
    g[++tot] = (edge){v, head[u], w}; head[u] = tot;
}

int n, k, m, key[maxn], ans;

int vis[maxn], siz[maxn], sum, mn, rt;
void getrt(int u, int f) {
    siz[u] = 1; int t = 0;
    for (int i = head[u]; i; i = g[i].nxt) {
        int v = g[i].to;
        if (v == f || vis[v]) continue;
        getrt(v, u); siz[u] += siz[v];
        t = max(t, siz[v]);
    }
    t = max(t, sum - siz[u]);
    if (t < mn) mn = t, rt = u;
}
int getrt(int u) {
    sum = siz[u]; mn = 1e9; rt = 0;
    getrt(u, 0); return rt;
}

vector<PII> vec;
void dfs(int u, int f, int c, int d) {
    c += key[u];
    if (c > k) return ;
    vec.push_back({c, d});
    for (int i = head[u]; i; i = g[i].nxt) {
        int v = g[i].to;
        if (v == f || vis[v]) continue;
        dfs(v, u, c, d + g[i].d);
    }
}
void solve(int u) {
    vis[u] = 1; vector<int> al; 
    update(key[u] + 1, 0); al.push_back(key[u]);
    for (int i = head[u]; i; i = g[i].nxt) {
        int v = g[i].to;
        if (vis[v]) continue;
        vec.clear();
        dfs(v, u, key[u], g[i].d);
        for (int i = 0; i < vec.size(); i++) {
            ans = max(ans, vec[i].second + query(k - vec[i].first + key[u] + 1));
        }
        for (int i = 0; i < vec.size(); i++) {
            al.push_back(vec[i].first), update(vec[i].first + 1, vec[i].second);
        }
    }
    for (int i = 0; i < al.size(); i++) del(al[i] + 1);
    for (int i = head[u]; i; i = g[i].nxt) {
        int v = g[i].to;
        if (vis[v]) continue;
        solve(getrt(v));
    }
}

int main() {
    scanf("%d%d%d", &n, &k, &m);
    for (int i = 1, x; i <= m; i++) scanf("%d", &x), key[x] = 1;
    for (int i = 2, u, v, w; i <= n; i++) {
        scanf("%d%d%d", &u, &v, &w);
        add(u, v, w); add(v, u, w);
    }
    siz[1] = n; solve(getrt(1));
    cout << ans << endl;
    return 0;
}
```
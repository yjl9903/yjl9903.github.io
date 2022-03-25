---
mathjax: true
title: Codeforces Round 548 题解
tags:
  - Codeforces
  - Solution
  - Tree
  - Math
  - Number Theory
  - Probability
  - Combinatorial
  - Graph
  - Network-Flow
categories:
  - Codeforces
date: 2019-03-22 04:14:10
---

久违的菜的真实的 <span style="color:blue;">XLor</span>。

# A Even Substrings

# B Chocolates

读错题是怎么回事？

# C Edgy Trees

给定一棵树，有一些关建边，求点集有多少大小为 $k$ 的有序子集，满足集合内存在一个相邻两点的树上路径经过关建边。

考虑反面，一个集合内不经过关建边，当且仅当这些点都在一个由非关建边连接而成的子联通块内。

连接所有非关建边，求一下每块大小即可。

# D Steps to One

给定整数 $m$，每次在 $[1,m]$ 内随机选择一个数 $x$ 加到序列内，当序列 $\gcd$ 为 $1$ 时，停止操作。求序列长度的期望。

考虑莫比乌斯反演。

对于因子 $d$，求序列 $\gcd$ 被 $d$ 整除的期望 $f(d)$，且序列至少选择一个 $d$ 倍数（否则有重复），令 $q=[m/d]/m$ 有下式

$$
f(d)=\sum_{i=1}^{\infty} (i+1) \cdot q^i \cdot (1-q)
$$

再特判一下 $d=1$ 的贡献即可。

# E Maximize Mex

给定 $m$ 个集合和 $n$ 个人，每个人属于集合 $c_i$， 有权值 $p_i$。

有 $d$ 次操作，每次删除一个人，求从每个集合内选出一个人组成新的集合的 $mex$ 最大值。

显然，将删除变成插入操作，且易知答案具有单调性。

考虑二分图匹配，左边的点集为 $m$ 个集合，右边的集合为权值 $p$，当且仅当集合有一个权值为 $p$ 的人时连边，且 $p$ 小于当前的答案。

对于每个答案，左边点集只会连向权值小于等于当前答案的边，如果此时最大匹配等于答案，那么显然存在一种分配方案。

我们不需要每次重新跑匹配，当二分图成功匹配后，表示存在一种派出方案，更新答案，并将答案对应的权值连边，继续在此基础上跑匹配。

<!--more-->

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

char s[maxn];
int n;

int main() {
    cin >> n;
    cin >> s + 1;
    ll ans = 0;
    for (int i = 1; i <= n; i++) if ((s[i] - '0') % 2 == 0) {
        ans += i;
    }
    cout << ans << endl;
    return 0;
}
```

## B

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 200000 + 5;

int n, a[maxn];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    ll ans = 0; int last = a[n] + 1;
    for (int i = n; i >= 1; i--) {
        if (last == 1) break;
        ans += min(last - 1, a[i]);
        last = min(last - 1, a[i]);
    }
    cout << ans << endl;
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 1e9 + 7;
const int inf = 1 << 30;
const int maxn = 200000 + 5;

ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}

vector<PII> edge[maxn];
int n, k, siz[maxn];
ll ans = 0;

void dfs(int u, int f, int k) {
    siz[u] = 1;
    for (auto& x: edge[u]) {
        int v = x.first;
        if (v == f) continue;
        if (x.second) {
            dfs(v, u, 1);
        } else {
            dfs(v, u, 0);
            siz[u] += siz[v];
        }
    }
    if (k) {
        ans += qpow(siz[u], ::k);
        if (ans >= mod) ans -= mod;
    }
}

int main() {
    scanf("%d%d", &n, &k);
    for (int i = 1, u, v, x; i < n; i++) {
        scanf("%d%d%d", &u, &v, &x);
        edge[u].push_back({v, x});
        edge[v].push_back({u, x});
    }
    dfs(1, 0, 1);
    cout << (qpow(n, k) - ans + mod) % mod << endl;
    return 0;
}
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 1e9 + 7;
const int inf = 1 << 30;
const int maxn = 200000 + 5;

int mu[maxn], vis[maxn], prime[maxn], tot;
void getMu() {
    mu[1] = 1;
    for (int i = 2; i < maxn; ++i) {
        if (!vis[i]) prime[++tot] = i, mu[i] = -1;
        for (int j = 1; j <= tot && i * prime[j] < maxn; ++j) {
            vis[i * prime[j]] = 1;
            if (i % prime[j] == 0) {
                mu[i * prime[j]] = 0;
                break;
            }
            mu[i * prime[j]] = -mu[i];
        }
    }
}

ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}
ll inv(ll x) {
    return qpow(x, mod - 2);
}

int m;
ll ans = 1;

int main() {
    getMu();
    cin >> m;
    ans = inv(m);
    for (int i = 2; i <= m; i++) {
        if (!mu[i]) continue;
        ll c = m / i, a = c * (2 * m - c) % mod, b = 1ll * m * (m - c);
        if (mu[i] > 0) ans -= a * inv(m) % mod * inv(m - c) % mod;
        else ans += a * inv(m) % mod * inv(m - c) % mod;
        ans += mod;
        ans %= mod;
    }
    cout << ans << endl;
    return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#include <queue>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const ll inf = 1ll << 60;
const int maxn = 20000 + 5;

struct Edge {
    int from, to; ll cap, flow;
    Edge(int u, int v, ll c, ll f): from(u), to(v), cap(c), flow(f) {}
};

struct Dinic {
    int n, m, s, t;
    vector<Edge> edges;
    vector<int> G[maxn];
    bool vis[maxn];
    int dep[maxn], cur[maxn];
    void init(int n) {
        this->n = n;
        for (int i = 0; i <= n; i++) G[i].clear();
        edges.clear();
    }
    void adde(int from, int to, ll cap) {
        edges.emplace_back(from, to, cap, 0);
        edges.emplace_back(to, from, 0, 0);
        m = edges.size();
        G[from].push_back(m - 2);
        G[to].push_back(m - 1);
    }
    bool bfs() {
        ms(vis, 0); ms(dep, 0);
        queue<int> q; q.push(s);
        dep[s] = 0; vis[s] = 1;
        while (!q.empty()) {
            int x = q.front(); q.pop();
            for (int i = 0; i < G[x].size(); i++) {
                Edge& e = edges[G[x][i]];
                if (!vis[e.to] && e.cap > e.flow) {
                    vis[e.to] = 1;
                    dep[e.to] = dep[x] + 1;
                    q.push(e.to);
                }
            }
        }
        return vis[t];
    }
    ll dfs(int x, ll a) {
        if (x == t || a == 0) return a;
        ll flow = 0, f;
        for (int& i = cur[x]; i < G[x].size(); i++) { 
            //从上次考虑的弧
            Edge& e = edges[G[x][i]];
            if (dep[x] + 1 == dep[e.to] && (f = dfs(e.to, min(a, e.cap - e.flow))) > 0) {
                e.flow += f;
                edges[G[x][i] ^ 1].flow -= f;
                flow += f;
                a -= f;
                if (a == 0) break;
            }
        }
        return flow;
    }
    ll get(int s, int t) {
        this->s = s, this->t = t;
        ll flow = 0;
        while (bfs()) {
            ms(cur, 0);
            flow += dfs(s, inf);
        }
        return flow;
    }
} f;

int n, m, d, del[maxn], p[maxn], c[maxn], op[maxn], ans[maxn];
vector<int> num[maxn];

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1, x; i <= n; i++) {
        scanf("%d", &x); num[x].push_back(i);
    }
    for (int i = 1; i <= n; i++) {
        scanf("%d", c + i);
    }
    scanf("%d", &d);
    for (int i = 1; i <= d; i++) {
        scanf("%d", op + i);
        del[op[i]] = 1;
    }
    for (int i = 1; i <= m; i++) f.adde(0, i, 1);
    for (int i = 1; i <= n; i++) if (!del[i]) {
        f.adde(c[i], m + i, 1);
    }
    int tot = 0, tag = n + m + 5005;
    for (int& x: num[tot]) {
        f.adde(m + x, n + m + 1 + tot, 1);
    }
    f.adde(n + m + 1 + tot, tag, 1);
    for (int i = d; i >= 1; i--) {
        while (f.get(0, tag)) {
            tot++; 
            for (int& x: num[tot]) f.adde(m + x, n + m + 1 + tot, 1);
            f.adde(n + m + 1 + tot, tag, 1);
            // cout << "? " << tot << endl;
        }
        ans[i] = tot;
        f.adde(c[op[i]], m + op[i], 1);
    }
    for (int i = 1; i <= d; i++) printf("%d\n", ans[i]);
    return 0;
}
```
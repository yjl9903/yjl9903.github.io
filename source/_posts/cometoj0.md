---
mathjax: true
title: Cometoj Round 0 题解
tags:
  - Cometoj
  - Solution
  - Number Theory
  - DP
  - Probability
  - Graph
  - Tree
  - Data Structure
categories:
  - Cometoj
date: 2019-04-05 22:21:53
---

被唐老师弄自闭了呀 T^T。

# A 解方程

给定自然数 $n$，求解 

$$
\sqrt{x-\sqrt{n}}+\sqrt{y}-\sqrt{z}=0
$$

不定方程方程所有自然数解 $(x,y,z)$ 之积求和，或者指出不定方程解有无穷多个。

首先注意到，若 $n$ 为完全平方数，有无穷多组解。

否则，尝试对 $x-\sqrt{n}$ 因式分解为平方式，对比系数可得

$$
\begin{cases}
x = y+{n \over 4y} \\
y = y \\
z = {n \over 4y}
\end{cases}
$$

因此，$n$ 必须是 $4$ 的倍数，之后枚举 ${n \over 4}$ 的每个因子算贡献即可，同时等价于求其约数个数和约数和。

此时，时间复杂度为 $O(T \cdot \sqrt{n})$，然而并不能通过。

此时可以用 $Pollard Rho$ 快速分解出质因数（误）。

预处理出 $10^5$ 以内的素数，枚举每个素因子试除，算贡献即可。

由于素数分布，素数个数除掉了一个 $\log$。

因此，时间复杂度为 $O(T \cdot {\sqrt{n} \over \log 10^9})$

# B 旅途

给定一个长度为 $n$ 的环，起点为 $1$，有 $m$ 天，每天顺时针走一步的概率为 $p$，逆时针走一步的概率为 $q$，否则原地停留。

令 $f(i)$ 表示最后一共游玩 $i$ 个城市的概率，求 $\sum_{i=1}^{n} i^k f(i)$。

若没有游玩所有城市，则游玩过的城市是环上的一个区间。

考虑 $dp[t][x][y]$ 表示第 $t$ 天，玩到顺时针 $x$ 个城市，逆时针 $y$ 个城市概率。

其中，$x,y \le 0$ 且 $x+y+1\le min(n-1,i)$。

初始状态 $dp[1][0][0] = 1$。

转移方程 

$$
dp[t][x+1][max(y-1,0)]+=dp[t-1][x][y] \cdot p \\
dp[t][max(x-1,0)][y+1]+=dp[t-1][x][y] \cdot q \\
dp[t][x][y] += dp[t-1][x][y] \cdot (100-p-q)
$$

# C 项链与计数

给了一个项链的定义，简单来说项链是由简单环 $c_1,c_2,\dots,c_n$ 按顺序连接的一条无向图路径，其中相邻两个有唯一公共点，其余均没有公共点和公共边。

给定 $m$ 条边，从没有边开始按顺序加边进去，对于每次加边操作，求出当前有多少点对之前存在一个项链。

观察这个定义，他的意思其实是将项链看成两条路径，一个项链其实是两点之间的多条不同路径，即双联通。

所以，题目询问的是每次操作后的所有双联通分量的大小，增量维护。

首先，扣出图上边编号最小的一棵生成树（森林），因为要保证树边是最先加入进来的。

然后，为森林标上根和每个点的父亲。

加边操作时，如果是树边，对答案无影响，否则，并查集维护每个双联通分量的大小。

加边操作对应两个点往上跳到公共祖先，将两条路径上的所有双联通分量合并。

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
#include <cstdlib>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 1e9 + 7;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

ll Prime[11] = {2, 3, 5, 7, 13, 17, 19, 23} ;

inline ll expow(ll a, ll b, ll p){
    ll res = 1 ;
    while (b){
        if (b & 1) 
            (res *= a) %= p ;
        (a *= a) %= p, b >>= 1 ; 
    }
    return res % p ;
}
inline bool Test(ll p, ll x){
    ll r = 0, d = x - 1 ;
    while (!(d & 1)) d >>= 1, ++ r ;
    for (ll i = expow(p, d, x), j ; r ; -- r){
        j = i * i % x ;
        if (j == 1){
            if (i == 1 || i == x - 1) 
                return 1 ; 
            return 0 ;
        }
        i = j ;
    }
    return 0 ;
}
inline bool Miller_Rabin(ll x){
    if (x == 1) return 0 ;
    for (int i = 0 ; i < 8 ; ++ i){
        if (x == Prime[i]) return 1 ;
        if (!(x % Prime[i])) return 0 ;
        if (!Test(Prime[i], x)) return 0 ;
    }
    return 1 ;
}
ll res[maxn], tot ;
inline int pksrand(){
    return rand() << 15 ^ rand() ;
}
inline ll Irand(ll x){
    return (((ll)pksrand()) << 30 ^ pksrand()) % x ; //2
}
inline ll guisuMul(ll a, ll b, ll x){
    return (a * b - (ll) ((long double) a * b / x + 1e-9) * x) % x;
} 
inline ll qwq(ll x){
    ll A = Irand(x), C = Irand(x) ;
    ll t1 = A, t2 = (guisuMul(A, A, x) + C) % x ; // 1
    ll dif = max(t1, t2) - min(t1, t2), G = __gcd(x, dif) ;
    while (G == 1){
        t1 = (guisuMul(t1, t1, x) + C) % x ;
        t2 = (guisuMul(t2, t2, x) + C) % x ;
        t2 = (guisuMul(t2, t2, x) + C) % x ;
        dif = max(t1, t2) - min(t1, t2), G = __gcd(x, dif) ;
    }
    return G ;
}
inline void Pollard_Rho(ll x){
    if (x == 1) return ;
    if (Miller_Rabin(x)) {
        res[++tot] = x; return ;
    }
    ll y = x ; while (y == x) y = qwq(x) ;
    Pollard_Rho(y), Pollard_Rho(x / y) ;
}

ll qpow(ll x, int n) {
    ll r = 1;
    while (n) {
        if (n & 1) r = r * x;
        n >>= 1; x = x * x;
    }
    return r;
}

int n;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        int sq = (int)sqrt(1.0 * n);
        if (sq * sq == n) {
            puts("infty"); continue;
        }
        if (n % 4) {
            puts("0 0"); continue;
        }
        tot = 0; n /= 4; ll m = n;
        Pollard_Rho(n);
        ll ans = 1; int cnt = 1;
        for (int i = 1; i <= tot; i++) {
            int c = 0;
            while (n % res[i] == 0) n /= res[i], c++;
            cnt *= c + 1;
            ll x = (qpow(res[i], c + 1) - 1) / (res[i] - 1); x %= mod;
            ans = ans * x % mod;
        }
        printf("%d %lld\n", cnt / 2, ans * m % mod);
    }
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
#ifdef XLor
  #define dbg(args...) do {cout << #args << " -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 1e9 + 7;
const int inf = 1 << 30;
const int maxn = 500 + 5;

ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}

int n, m, k, p, q;
ll dp[2][maxn][maxn];

void add(ll& x, ll y) {
    x += y; if (x >= mod) x -= mod;
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        ms(dp, 0);
        scanf("%d%d%d%d%d", &n, &m, &k, &p, &q);
        int f = 0; dp[f][0][0] = 1;
        for (int i = 1; i < m; i++, f ^= 1) {
            ms(dp[f ^ 1], 0);
            for (int x = 0; x < n; x++) {
                for (int y = 0; x + y + 1 <= min(n - 1, i + 1); y++) {
                    add(dp[f ^ 1][x + 1][max(y - 1, 0)], dp[f][x][y] * p % mod);
                    add(dp[f ^ 1][max(x - 1, 0)][y + 1], dp[f][x][y] * q % mod);
                    add(dp[f ^ 1][x][y], dp[f][x][y] * (100 - p - q) % mod);
                }
            }
        }
        ll ans = 0, sum = 0;
        for (int x = 0; x < n; x++) {
            for (int y = 0; x + y + 1 <= min(n - 1, m); y++) {
                add(ans, qpow(x + y + 1, k) * dp[f][x][y] % mod);
                add(sum, dp[f][x][y]);
            }
        }
        ll pr = (qpow(100, m - 1) - sum + mod) % mod;
        // dbg(sum, pr);
        add(ans, pr * qpow(n, k) % mod);
        printf("%lld\n", ans);
    }
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
#include <queue>
#include <functional>
#include <assert.h>
#ifdef XLor
  #define dbg(args...) do {cout << #args << " -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 2000000 + 5;

int n, m, mark[maxn], vis[maxn];
ll ans, tot;
vector<PII> edge[maxn];
PII g[maxn];

int dep[maxn], fa[maxn], pre[maxn], siz[maxn];
void dfs(int u, int f) {
    vis[u] = 1;
    fa[u] = f;
    dep[u] = dep[f] + 1;
    for (auto& x: edge[u]) {
        int v = x.first;
        if (vis[v]) continue;
        if (!mark[x.second]) continue;
        // mark[x.second] = 1;
        dfs(v, u);
    }
}

int find(int x) { return x == pre[x] ? x : pre[x] = find(pre[x]); }
ll cal(ll x) {
    return x * (x - 1) / 2;
}

struct node {
    int u, v, id;
    bool operator<(const node& b) const {
        return id > b.id;
    }
};
void kruskal() {
    priority_queue<node> pq;
    for (int i = 1; i <= m; i++) pq.push({g[i].first, g[i].second, i});
    while (!pq.empty()) {
        node x = pq.top(); pq.pop();
        int u = find(x.u), v = find(x.v);
        if (u == v) continue;
        mark[x.id] = 1;
        pre[u] = v;
    }
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        for (int i = 1; i <= n; i++) {
            edge[i].clear(); 
            vis[i] = 0; pre[i] = i; siz[i] = 1;
        }
        for (int i = 1; i <= m; i++) {
            mark[i] = 0;
            scanf("%d%d", &g[i].first, &g[i].second);
            edge[g[i].first].push_back({g[i].second, i});
            edge[g[i].second].push_back({g[i].first, i});
        }
        kruskal();
        for (int i = 1; i <= n; i++) if (!vis[i]) dfs(i, 0);
        for (int i = 1; i <= n; i++) pre[i] = i;
        ans = tot = 0;
        for (int i = 1; i <= m; i++) {
            if (mark[i]) {
                dbg(i, tot);
                ans ^= tot * i; continue;
            }
            int u = find(g[i].first), v = find(g[i].second);
            vector<int> vec;
            while (u != v) {
                if (dep[u] < dep[v]) swap(u, v);
                vec.push_back(u);
                u = find(fa[u]);
            }
            assert(u == v);
            tot -= cal(siz[u]);
            for (int& x: vec) {
                if (x == u) continue;
                tot -= cal(siz[x]);
                pre[x] = u;
                siz[u] += siz[x];
            }
            tot += cal(siz[u]);
            dbg(i, tot, u, siz[u]);
            ans ^= tot * i;
        }
        printf("%lld\n", ans);
    }
    return 0;
}
```
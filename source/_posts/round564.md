---
mathjax: true
title: Codeforces Round 564 题解
tags:
  - Codeforces
  - Solution
  - DP
  - Tree
  - Probability
  - Combinatorial
categories:
  - Codeforces
date: 2019-06-08 23:40:38
---

# A Nauuo and Votes

# B Nauuo and Chess

有 $n$ 个棋子，你要找一个最小的 $m \times m$ 的棋盘，放下这些棋子，要求两两曼哈顿距离大于下标之差的绝对值。

容易构造出沿着棋盘的上边和右边边缘放旗子即可。

# C Nauuo and Cards

有 $n$ 张牌，编号为 $1$ 到 $n$，还有 $n$ 张空牌，现在你手上有 $n$ 张牌，桌上有 $n$ 张牌，你可以从桌上牌堆顶部摸一张，在放回一张到牌堆底部。

问最小操作次数，使得桌上按顺序为 $1,2,\dots,n$。

假如 $1$ 在手上，需要枚举出首先放几张空牌到桌上即可，其他情况类似讨论即可。

# D Nauuo and Circle

给定一棵无根树，每个点编号为 $1$ 到 $n$，你现在需要将这棵树画在一个环上，满足环内不能有交叉。

固定根节点为 $1$，注意到一棵子树必须占据一段连续区间，一个点的儿子可以任意排列，每个儿子可以选择其度数个位置，容易 Tree DP。

# E Nauuo and Pictures

给定 $n$ 个物品，每个物品有一个权值和是否被喜欢，做 $m$ 次操作，随机选择一个物品，其被选择的概率是其权值比上权值和，如果这个物品被喜欢则其权值加一，反之减一，求最后每个物品的权值期望。

注意到一个事实，每个物品的权值期望是正比于其权值的，因此只需要考虑权值为 $1$ 的情况即可。

相关证明 [Codeforces Round #564 中文题解](https://ouuan.github.io/Codeforces-Round-564-%E4%B8%AD%E6%96%87%E9%A2%98%E8%A7%A3/#more)。

也可以理解，把一个物品拆成了 $w$ 个。因此可以看成两个巨大物品，喜欢和不喜欢两种总和。

对 $m$ 次操作做 $\mathcal{O}(m^2)$ 的 $dp$。

记 $f(i,j)$ 表示前 $i$ 次操作做了 $j$ 次加操作，$f(i,j)$ 可以转移到 $f(i+1,j+1)$ （选择一个喜欢的物品）或者 $f(i+1,j)$ （选择一个不喜欢的物品），除上概率。

最后，对于每个物品，根据其是否喜欢，在两种中权值的比重乘上前面计算的系数即可。

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

int x, y, z;

int main() {
    cin >> x >> y >> z;
    int f1 = 0, f2 = 0, f3 = 0;
    if (x + z > y) f1 = 1;
    if (y + z > x) f2 = 1;
    if (abs(x - y) <= z) f3 = 1;
    int sum = f1 + f2 + f3;
    if (sum > 1) puts("?");
    else if (f1) puts("+");
    else if (f2) puts("-");
    else puts("0");
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
const int maxn = 1000 + 5;

int n;

int main() {
    cin >> n;
    int m = (n + 1) / 2 + (n + 1) % 2;
    cout << m << '\n';
    int c = 1;
    for (int i = 1; c <= n && i <= m; c++, i++) {
        printf("%d %d\n", 1, i);
    }
    for (int i = 2; c <= n && i <= m; c++, i++) {
        printf("%d %d\n", i, m);
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
const int maxn = 200000 + 5;

int n, a[maxn], b[maxn];

int main() {
    scanf("%d", &n);
    int my = 0, dk = 0;
    for (int i = 1; i <= n; i++) {
        scanf("%d", a + i);
        if (a[i] == 1) my = i;
    }
    for (int i = 1; i <= n; i++) {
        scanf("%d", b + i);
        if (b[i] == 1) dk = i;
    }
    if (my) {
        int mx = 0;
        for (int i = 1; i <= n; i++) {
            if (b[i] == 0) continue;
            mx = max(mx, i - b[i] + 1);
        }
        printf("%d", mx + n);
    } else if (dk == n) {
        int f = 1;
        for (int i = 1; i < n; i++) {
            if (b[i] == 0) continue;
            if (b[i] - 2 < i) {
                f = 0; break;
            }
        }
        if (f) printf("%d", n - 1);
        else printf("%d", n + n);
    } else {
        int f = 1;
        for (int i = dk + 1; i <= n; i++) {
            if (b[i] == 0) {
                f = 0; break;
            }
            if (b[i] - b[i - 1] != 1) {
                f = 0; break;
            }
        }
        if (f) {
            int f = 1;
            int cur = n - dk + 1;
            for (int i = 1; i < dk; i++) {
                if (b[i] == 0) continue;
                // dbg(b[i], cur);
                if (b[i] - cur - 1 < i) {
                    f = 0; break;
                }
            }
            if (f) printf("%d", dk - 1);
            else printf("%d", n + dk);
        } else {
            int mx = 0;
            for (int i = dk + 1; i <= n; i++) {
                if (b[i] == 0) continue;
                // dbg(i - b[i] - dk + 1);
                mx = max(mx, i - b[i] - dk + 1);
            }
            // dbg(mx);
            printf("%d", dk + n + mx);
        }
    }
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
const int maxn = 200000 + 5;

int n, f[maxn], siz[maxn];
vector<int> edge[maxn];

int dfs(int u, int ff) {
    siz[u] = 1;
    int r = 1;
    for (int& v: edge[u]) {
        if (v == ff) continue;
        r = 1ll * r * dfs(v, u) % mod;
        // if (siz[v] > 1) r = 2ll * r % mod;
        r = 1ll * r * (int)(edge[v].size()) % mod;
        siz[u] += siz[v];
    }
    dbg(edge[u].size());
    int sz = (int)edge[u].size(); if (u != 1) sz--;
    return 1ll * r * f[sz] % mod;
}

int main() {
    f[0] = 1;
    for (int i = 1; i < maxn; i++) f[i] = 1ll * i * f[i - 1] % mod;
    scanf("%d", &n);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    int ans = dfs(1, 0);
    cout << 1ll * n * ans % mod;
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
const int maxn = 200000 + 5;
const int maxq = 3000 + 5;

ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}
ll inv(int x) {
    return qpow(x, mod - 2);
}
void add(ll& x, ll y) {
    x += y; if (x >= mod) x -= mod;
}

int n, m, a[maxn], w[maxn];
ll f[maxq][maxq];

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    int sa = 0, sb = 0;
    for (int i = 1; i <= n; i++) {
        scanf("%d", w + i);
        if (a[i]) sa += w[i];
        else sb += w[i];
    }
    f[0][0] = 1;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j <= i; j++) {
            int a = sa + j, b = sb - (i - j);
            ll fm = inv(a + b);
            add(f[i + 1][j + 1], f[i][j] * a % mod * fm % mod);
            add(f[i + 1][j], f[i][j] * b % mod * fm % mod);
        }
    }
    ll ea = 0, eb = 0;
    for (int i = 0; i <= m; i++) {
        dbg(f[m][i]);
        add(ea, f[m][i] * (sa + i) % mod);
        add(eb, f[m][i] * (sb - (m - i)) % mod);
    }
    dbg(ea, eb);
    ll iva = inv(sa), ivb = inv(sb);
    for (int i = 1; i <= n; i++) {
        if (a[i]) {
            printf("%lld\n", 1ll * w[i] * iva % mod * ea % mod);
        } else {
            printf("%lld\n", 1ll * w[i] * ivb % mod * eb % mod);
        }
    }
    return 0;
}
```
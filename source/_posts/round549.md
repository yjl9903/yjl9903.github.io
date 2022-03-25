---
mathjax: true
title: Codeforces Round 549 题解
tags:
  - Codeforces
  - Solution
  - DP
  - Number Theory
  - Tree
categories:
  - Codeforces
date: 2019-04-09 15:40:16
---

# A The Doors

# B Nirvana

# C Queen

给定一棵 $n$ 个点的有根树，每个点有一个权值 $c_i$ 表示是否尊重它的父亲，删除满足它自己不尊重父亲且它的孩子也不尊重它的的结点，然后将这个点的儿子连向该点的父亲，直到不能删除为止，如果同时有多个点可以删除，则删除标号最小的点。

$dfs$ 扫一遍，维护一下每个点被多少个儿子尊重，将所有尊重数为 $0$ 且自己不尊重父亲的点拿出来排序即可。

# D The Beatles

给定一个长度为 $n\cdot k$ 的环，环上编号为 $1,k+1,2k+1,\dots$ 的点为汉堡王，一个人从环上某个点出发，每次走 $l$ 步直到回到起点。

我们不知道出发点和步长，只知道一开始距离汉堡王的最近距离，以及跳一步后与最近的汉堡王距离。

求最少和最多跳多少次回到起点。

随便枚举一下起点和终点，注意到起点并不重要，只有相对位置重要，对于步长 $l$，跳的步数为 $nk \over \gcd(nk,l)$。

# E Lynyrd Skynyrd

给定 $1$ 到 $n$ 的排列 $p$，给定一个长度为 $m$ 的序列 $a$，每次询问 $a$ 的子串是否含有一个子序列是 $p$ 的一个循环串。

对于序列 $a$ 内的每个位置，搞出其下一步跳到哪里为在 $p$ 串上对应的下一个位置。

考虑倍增，设 $dp[i][j]$ 表示第 $i$ 个位置在 $p$ 上跳 $2^j$ 个到达的位置。

之后容易得到，每个点跳 $n-1$ 步到达的位置，即从某一个位置开始，最短一个子串，包含了子序列是 $p$ 的一个循环串。

对于每个询问 $[l,r]$，即在这个区间内选一个右端点最小的子串满足条件，如果这个右端点在 $r$ 的左边，即含有这样的循环串，反之不含有，这部分也可以预处理得到。

时间复杂度：$O(m\log n + q)$。

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
const int maxn = 200000 + 5;

int n, a[maxn];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    int p = 1;
    for (int i = 1; i < n; i++) if (a[i] != a[n]) p = i;
    cout << p << endl;
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
const int maxn = 100000 + 5;

int n;
char s[30], t[30];

ll cal(int x) {
    ll ans = 1;
    while (x) ans *= x % 10, x /= 10;
    return ans;
}

int main() {
    // cin >> n;
    cin >> s;
    if (strlen(s) == 1) return puts(s), 0;
    ll ans = 1;
    t[0] = s[0] - 1;
    for (int j = 1; s[j]; j++) t[j] = '9';
    for (int j = 0; t[j]; j++) {
        if (t[j] == '0') continue;
        ans *= t[j] - '0';
    }
    ll tot = 1;
    for (int i = 0; s[i]; i++) tot *= s[i] - '0';
    ans = max(tot, ans);
    for (int i = 1; s[i]; i++) {
        if (s[i] <= '1') continue;
        for (int j = 0; j < i; j++) t[j] = s[j];
        t[i] = s[i] - 1;
        for (int j = i + 1; s[j]; j++) t[j] = '9';
        ll tot = 1;
        for (int j = 0; t[j]; j++) {
            if (t[j] == '0') continue;
            tot *= t[j] - '0';
        }
        ans = max(ans, tot);
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
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

vector<int> edge[maxn];
int n, rt, fa[maxn], key[maxn], siz[maxn], vis[maxn];

void dfs(int u) {
    for (int& v: edge[u]) {
        siz[u] += key[v];
        dfs(v);
    }
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%d%d", fa + i, key + i);
        key[i] ^= 1;
        if (fa[i] == -1) rt = i;
        else {
            edge[fa[i]].push_back(i);
        }
    }
    dfs(rt);
    vector<int> ans; set<int> st;
    for (int i = 1; i <= n; i++) if (siz[i] == 0 && key[i] == 0) st.insert(i);
    // for (int i = 1; i <= n; i++)  cout << siz[i] << endl;
    while (!st.empty()) {
        auto it = st.begin();
        if (*it == rt) break;
        ans.push_back(*it);
        vis[*it] = 1;
        siz[fa[*it]] -= key[*it];
        if (fa[*it] != -1) {
            if (key[fa[*it]] == 0 && !vis[fa[*it]] && siz[fa[*it]] == 0) st.insert(fa[*it]);
        }
        st.erase(it);
    }
    if (ans.empty()) puts("-1");
    else {
        for (int& x: ans) printf("%d ", x);
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
const int maxn = 100000 + 5;

ll gcd(ll a, ll b) {return b ? gcd(b, a % b) : a;}

int n, k, a, b;

ll cal(ll st, ll ed) {
    ll l = ed - st;
    if (l <= 0) l += 1ll * n * k;
    ll g = gcd(l, 1ll * n * k);
    return 1ll * n * k / g;
}

int main() {
    cin >> n >> k >> a >> b;
    ll x = 1e18, y = -1e18;
    for (ll i = 1 + b; i <= 1ll * n * k; i += k) {
        // dbg(1 + a, 1 + k - a, i);
        ll p = cal(1 + a, i);
        ll q = cal(1 + k - a, i);
        x = min(min(p, q), x);
        y = max(max(p, q), y);
    }
    for (ll i = 1 + k - b; i <= 1ll * n * k; i += k) {
        ll p = cal(1 + a, i);
        ll q = cal(1 + k - a, i);
        x = min(min(p, q), x);
        y = max(max(p, q), y);
    }
    cout << x << ' ' << y << endl;
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

int n, m, q, p[maxn], a[maxn];
int dp[maxn][25], ed[maxn], pos[maxn], mp[maxn], rt[maxn];

int main() {
    scanf("%d%d%d", &n, &m, &q);
    for (int i = 1; i <= n; i++) scanf("%d", p + i), pos[p[i]] = i;
    for (int i = 1; i <= m; i++) scanf("%d", a + i);
    for (int i = m; i >= 1; i--) {
        int v = pos[a[i]] + 1; if (v == n + 1) v = 1;
        if (mp[p[v]]) {
            dp[i][0] = mp[p[v]];
        }
        // dbg(i, dp[i][0]);
        mp[a[i]] = i;
    }
    for (int l = 1; l <= 20; l++) {
        for (int i = 1; i <= m; i++) {
            dp[i][l] = dp[dp[i][l - 1]][l - 1];
        }
    }
    for (int i = 1; i <= m; i++) {
        int x = i;
        for (int j = 0; j <= 20; j++) {
            if ((n - 1) & (1 << j)) x = dp[x][j];
        }
        ed[i] = x;
    }
    for (int i = m; i >= 1; i--) {
        if (!ed[i]) {
            rt[i] = rt[i + 1];
        } else {
            if (rt[i + 1]) rt[i] = min(rt[i + 1], ed[i]);
            else rt[i] = ed[i];
        }
        dbg(i, ed[i], rt[i]);
    }
    int l, r;
    while (q--) {
        scanf("%d%d", &l, &r);
        if (r - l + 1 < n || !rt[l]) {
            putchar('0'); continue;
        }
        if (rt[l] <= r) putchar('1');
        else putchar('0');
    }
    return 0;
} 
```
---
mathjax: true
title: Educational Round 68 题解
tags:
  - Codeforces
  - Solution
  - Combinatorial
  - Data Structure
  - Fenwick Tree
categories:
  - Codeforces
  - Educational Round
date: 2019-07-15 13:37:20
---

# A Remove a Progression

# B Yet Another Crosses Problem

# C From S To T

# D 1-2-K Game

两个人游戏，从 $n$ 数到 $0$，每次选择 $-1,-2,-k$ 三种中一个，无法操作者为负，判断是否先手必胜。

必胜态 / 必败态打表观察。

# E Count The Rectangles

给了一堆水平线和竖直线，求能够拼成多少个矩形。

枚举右边界，扣出与右边界相交的横线，枚举左边界，从左到右扫描线，树状数组维护横线的纵坐标出现位置。

时间复杂度 $\mathcal{O}(n^2 \log n)$。

# F Crossword Expert

总时间为 $T$，依次发生 $n$ 个事件，每个事件时长为 $t_i$，每个事件的时长各有一半的概率不变和变成 $t_i+1$，求发生事件次数的期望。

设发生事件 $i$ 至少需要时间 $pre$，即前缀和，最多花费时间 $pre+i$，那么事件 $i$ 发生的概率为 $\sum_{j=0}^{T-pre}{ i \choose j } / 2^i$。

因此题意变成求一堆这样的组合数，~~分治 NTT~~ 莫队即可。

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
 
int n, x;
 
int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &x);
        printf("%d\n", x * 2);
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
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 500000 + 5;
 
int n, m, cn[maxn], cm[maxn];
set<PII> mp;
char s[maxn];
 
int main() {
    int T; scanf("%d", &T);
    while (T--) {
        mp.clear();
        scanf("%d%d", &n, &m);
        for (int i = 1; i <= n; i++) cn[i] = 0;
        for (int i = 1; i <= m; i++) cm[i] = 0;
        for (int i = 1; i <= n; i++) {
            scanf("%s", s + 1);
            for (int j = 1; j <= m; j++) {
                if (s[j] == '*') cn[i]++, cm[j]++, mp.insert({i, j});
            }
        }
        int ans = maxn;
        for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++) {
            ans = min(ans, n + m - 1 - (cn[i] + cm[j] - (int)mp.count({i, j})));
        }
        printf("%d\n", ans);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 1000 + 5;
 
char s[maxn], t[maxn], p[maxn];
int slen, tlen, plen, nxt[maxn][26], vis[maxn], cnt[26];
 
int main() {
    int T; scanf("%d", &T);
    while (T--) {
        ms(vis, 0); ms(cnt, 0);
        scanf("%s%s%s", s + 1, t + 1, p + 1);
        slen = strlen(s + 1);
        tlen = strlen(t + 1);
        plen = strlen(p + 1);
        for (int i = 0; i < 26; i++) nxt[tlen + 1][i] = nxt[tlen][i] = tlen + 1;
        for (int i = tlen - 1; i >= 0; i--) {
            for (int j = 0; j < 26; j++) nxt[i][j] = nxt[i + 1][j];
            nxt[i][t[i + 1] - 'a'] = i + 1; 
        }
        int u = 0;
        for (int i = 1; i <= slen; i++) {
            u = nxt[u][s[i] - 'a']; vis[u] = 1;
        }
        if (u == tlen + 1) {
            puts("NO"); continue;
        }
        for (int i = 1; i <= tlen; i++) if (!vis[i]) {
            cnt[t[i] - 'a']++;
        }
        for (int i = 1; i <= plen; i++) {
            cnt[p[i] - 'a']--;
        }
        int f = 1;
        for (int i = 0; i < 26; i++) if (cnt[i] > 0) f = 0;
        if (f) puts("YES");
        else puts("NO");
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
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;
 
int n, k;
 
map<int,int> sg;
int dfs(int u) {
    if (sg.count(u)) return sg[u];
    if (u < k) return u % 3 != 0;
    int f1 = dfs(u - 1), f2 = dfs(u - 2), f3 = dfs(u - k);
    if (!f1 || !f2 || !f3) return sg[u] = 1;
    return sg[u] = 0;
}
 
int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &k);
        if (k > n) {
            if (n % 3 == 0) puts("Bob");
            else puts("Alice");
        } else if (k == n) {
            puts("Alice");
        } else {
            // sg.clear();
            // sg[0] = 0; sg[1] = 1; sg[2] = 1; sg[k] = 1;
            // if (dfs(n)) puts("Alice");
            // else puts("Bob");
            if (k % 3 == 0) {
                int x = n % (k + 1);
                if (x == k) puts("Alice");
                else {
                    if (x % 3 == 0) puts("Bob");
                    else puts("Alice");
                }
            } else if (k % 3 == 1) {
                if (n % 3 == 0) puts("Bob");
                else puts("Alice");
            } else {
                if (n % 3 == 0) puts("Bob");
                else puts("Alice");
            }
        }
    }
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int offset = 5001;
const int maxn = 100000 + 20;
 
int tr[maxn];
void update(int i, int x) {
    for (; i < maxn; i += i & -i) tr[i] += x;
}
int query(int i) {
    int r = 0;
    for (; i; i -= i & -i) r += tr[i];
    return r;
}
 
struct SegX {
    int x, y1, y2;
    bool operator<(const SegX& b) const {
        return x < b.x;
    }
};
struct SegY {
    int y, x1, x2;
};
struct Event {
    int x, y, tp;
    bool operator<(const Event& b) const {
        return x < b.x;
    }
};
 
int n;
vector<SegX> vx;
vector<SegY> vy;
 
int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        int x1, y1, x2, y2;
        scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
        x1 += offset; y1 += offset;
        x2 += offset; y2 += offset;
        if (x1 == x2) {
            if (y1 > y2) swap(y1, y2);
            vx.push_back({x1, y1, y2});
        } else {
            if (x1 > x2) swap(x1, x2);
            vy.push_back({y1, x1, x2});
        }
    }
    sort(vx.begin(), vx.end());
    int xn = (int)vx.size(), yn = (int)vy.size();
    ll ans = 0;
    for (int i = 0; i < xn; i++) {
        vector<Event> ev;
        int tx = vx[i].x, ty1 = vx[i].y1, ty2 = vx[i].y2;
        for (auto& seg: vy) {
            if (seg.x1 <= tx && tx <= seg.x2 && ty1 <= seg.y && seg.y <= ty2) {
                ev.push_back({seg.x1, seg.y, 1});
            }
        }
        sort(ev.begin(), ev.end());
        int sz = (int)ev.size(), tot = -1;
        for (int j = 0; j < i; j++) {
            while (tot + 1 < sz && ev[tot + 1].x <= vx[j].x) {
                tot++; update(ev[tot].y, 1);
            }
            int cnt = query(vx[j].y2) - query(vx[j].y1 - 1);
            ans += 1ll * cnt * (cnt - 1) / 2ll;
        }
        for (int i = 0; i <= tot; i++) update(ev[i].y, -1);
    }
    cout << ans;
    return 0;
}
```

## F

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
const int sz = 450;
const int maxn = 200000 + 5;
 
int qpow(ll x, ll n) {
    int r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}
int f[maxn], inv[maxn], finv[maxn];
void init(){
    inv[1] = 1;
    for (int i = 2; i < maxn; i++) inv[i] = (mod - mod / i) * 1ll * inv[mod % i] % mod;
    f[0] = finv[0] = 1; 
    for (int i = 1; i < maxn; i++) {
        f[i] = f[i - 1] * 1ll * i % mod;
        finv[i] = finv[i - 1] * 1ll * inv[i] % mod;
    }
}
int C(int n, int m){
    if (m < 0 || m > n) return 0;
    return f[n] * 1ll * finv[n - m] % mod * finv[m] % mod;
}
void add(int& x, int y) {
    x += y; if (x >= mod) x -= mod;
}
void sub(int& x, int y) {
    x -= y; if (x < 0) x += mod;
}
void mul(int& x, int y) {
    x = 1ll * x * y % mod;
}
const int inv2 = qpow(2, mod - 2);
 
struct Que {
    int n, m;
    bool operator<(const Que& b) const {
        if (n / sz == b.n / sz) {
            if (n / sz % 2) return m > b.m;
            return m < b.m;
        }
        return n < b.n;
    }
};
vector<Que> q;
 
int n, t[maxn]; ll T;
 
int main() {
    init();
    ll pre = 0; int ans = 0;
    scanf("%d%I64d", &n, &T);
    for (int i = 1; i <= n; i++) {
        scanf("%d", t + i);
        pre += t[i];
        if (pre > T) continue;
        if (pre + i <= T) ans++;
        else q.push_back({ i, int(T - pre) });
    }
    sort(q.begin(), q.end());
    int nn = 1, mm = 0, tot = 1;
    for (auto x: q) {
        while (nn < x.n) {
            add(tot, tot); sub(tot, C(nn, mm)); nn++;
        }
        while (nn > x.n) {
            nn--; add(tot, C(nn, mm)); mul(tot, inv2);
        }
        while (mm < x.m) {
            mm++; add(tot, C(nn, mm));
        }
        while (mm > x.m) {
            sub(tot, C(nn, mm)); mm--;
        }
        add(ans, 1ll * tot * qpow(inv2, x.n) % mod);
    }
    cout << ans;
    return 0;
}
```
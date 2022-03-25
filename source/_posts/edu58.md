---
mathjax: true
title: Educational Round 58 题解
tags:
- Codeforces
- Solution
- Tree
- Math
categories:
- Codeforces
- Educational Round
date: 2019-03-01 20:12:41
---

# A Minimum Integer

# B Accordion

# C Division and Union

给定 $n$ 个线段，将线段分成两个集合，两个集合之间的线段没有交集。

按左端点排序，将和第一个相交的一整段抠出来，剩余放在第二个集合内。

# D GCD Counting

求树上最长路径，满足路径上点权的 $\gcd$ 大于 $1$。

点分治一下。

点权不是很大，考虑把路径的质因子抠出来 $dp$。

# E Polycarp's New Job

# G (Zero XOR Subset)-less

给定一个长度为 $n$ 的序列，将序列分成最多线段，满足线段集合的任意子集没有异或和为 $0$。

转换成前缀和，即变成选择最多的单点，且必须选择最后一个点，使得任意子集异或和不为 $0$，即线性无关，线性基插入即可。

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
const int maxn = 100000 + 5;

int l, r, d;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d%d", &l, &r, &d);
        if (l > d) printf("%d\n", d);
        else {
            int k = r / d + 1;
            printf("%I64d\n", 1ll * k * d); 
        }
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 500000 + 5;

char s[maxn]; int n;

int main() {
    scanf("%s", s);
    int n = strlen(s), l = -1, r = -1, l2 = -1, r2 = -1;
    for (int i = 0; i < n; i++) {
        if (s[i] == '[') {
            l = i; break;
        } 
    }
    for (int i = n - 1; i >= 0; i--) {
        if (s[i] == ']') {
            r = i; break;
        }
    }
    if (l == -1 || r == -1) return puts("-1"), 0;
    for (int i = l; i <= r; i++) {
        if (s[i] == ':') {
            if (l2 == -1) l2 = i;
            else r2 = i;
        }
    }
    if (l2 == -1 || r2 == -1) return puts("-1"), 0;
    int cnt = 0;
    for (int i = l2; i <= r2; i++) if (s[i] == '|') cnt++;
    printf("%d", cnt + 4);
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
const int maxn = 100000 + 5;

struct node {
    int l, r, id;
    bool operator<(const node& b) const {
        if (l == b.l) return r < b.r;
        return l < b.l;
    }
} a[maxn];

int n, ans[maxn];

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        for (int i = 0; i < n; i++) scanf("%d%d", &a[i].l, &a[i].r), a[i].id = i, ans[i] = 0;
        sort(a, a + n);
        int r = a[0].r, flag = 1; ans[a[0].id] = 1;
        for (int i = 1; i < n; i++) {
            if (a[i].l > r) {
                flag = 0; break;
            }
            ans[a[i].id] = 1;
            r = max(r, a[i].r);
        }
        if (flag) {
            puts("-1"); continue;
        }
        for (int i = 0; i < n; i++) if (ans[i]) printf("1 "); else printf("2 ");
        puts("");
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
#define assert(x) do{int a=1,b=0;if(!(x))printf("%d",a/b);}while(0)
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
const int maxn = 200000 + 5;

int gcd(int a, int b){return b ? gcd(b, a % b) : a;}

vector<int> edge[maxn], fac[maxn];
int n, ans, a[maxn], dep[maxn];

int vis[maxn], siz[maxn], sum, mn, rt;
void getrt(int u, int f) {
    siz[u] = 1; int t = 0;
    for (int& v: edge[u]) {
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

int g[maxn], mp[maxn]; vector<int> vec;
void dfs(int u, int f) {
    g[u] = gcd(a[u], g[f]);
    if (g[u] == 1) return ;
    dep[u] = dep[f] + 1;
    vec.push_back(u);
    for (int& v: edge[u]) {
        if (v == f || vis[v]) continue;
        dfs(v, u);
    }
}
void solve(int u) {
    dbg(u);
    vis[u] = 1; g[u] = a[u]; dep[u] = 0;
    for (int& x: fac[a[u]]) mp[x] = 0, ans = max(ans, 1);
    vector<int> al; al.push_back(a[u]);
    for (int& v: edge[u]) {
        if (vis[v]) continue;
        vec.clear(); dfs(v, u);
        for (int& x: vec) {
            dbg(x, g[x]);
            for (int& y: fac[g[x]]) ans = max(ans, mp[y] + dep[x] + 1);
        }
        for (int& x: vec) {
            al.push_back(g[x]);
            for (int& y: fac[g[x]]) mp[y] = max(mp[y], dep[x]);
        }
    }
    for (int& x: al) {
        for (int& y: fac[x]) mp[y] = -1e9;
    }
    for (int& v: edge[u]) {
        if (vis[v]) continue;
        solve(getrt(v));
    }
}

int main() {
    for (int i = 2; i < maxn; i++) {
        vector<int> v; int x = i;
        for (int j = 2; j * j <= x; j++) {
            if (x % j) continue;
            while (x % j == 0) x /= j;
            v.push_back(j);
        }
        if (x > 1) v.push_back(x);
        fac[i] = v; mp[i] = -1e9;
    }
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    siz[1] = n; solve(getrt(1));
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

char op[2]; int x, y, mx, my;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%s%d%d", op, &x, &y);
        if (op[0] == '+') {
            if (x > y) swap(x, y);
            mx = max(mx, x); my = max(my, y);
        }
        if (op[0] == '?') {
            if (x >= mx && y >= my) puts("YES");
            else if (x >= my && y >= mx) puts("YES");
            else puts("NO");
        }
    }
    return 0;
}
```

## G

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
const int maxn = 200000 + 5;

struct LinearBase {
    static const int maxl = 63;
    ll a[maxl + 5];
    int cnt;
    LinearBase() { cnt=0; ms(a, 0); }
    void clear() { cnt=0; ms(a, 0); }
    void insert(ll x) {
        for (int i = maxl - 1; i >= 0; i--) {
            if (x & (1ll << i)) {
                if (a[i]) x ^= a[i];
                else {
                    for (int k = 0; k < i; k++) 
                        if (x & (1ll << k)) x ^= a[k];
                    for (int k = i + 1; k < maxl; k++) 
                        if (a[k] & (1ll << i)) a[k] ^= x;
                    a[i] = x; cnt++;
                    return ;
                }
            }
        }
    }
    bool check(ll x) {
        for (int i = maxl - 1; i >= 0; i--) {
            if (x >> i & 1) {
                if (a[i]) x ^= a[i];
                else return false;
            }
        }
        return true;
    }
    ll qmax(int x) {
        ll res = x;
        for(int i = maxl - 1 ; i >= 0; i--) {
            if ((res ^ a[i]) > res) res ^= a[i];
        }
        return res;
    }
    // #define QUERY_KTH
    #ifdef QUERY_KTH
    vector<ll> v;
    void init_kth() {
        v.clear();
        for (int i = 0; i < maxl; i++) if (a[i]) v.push_back(a[i]);
    }
    ll query(ll k){
        if (v.size() != n) k--;
        if (k >= (1ll << v.size())) return -1;
        ll ans = 0;
        for (int i = 0; i < v.size(); i++) if (k & (1ll << i))
            ans ^= v[i];
        return ans;
    }
    #endif
} f;

int n, a[maxn];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i), a[i] ^= a[i - 1];
    if (a[n] == 0) return puts("-1"), 0;
    for (int i = n; i >= 1; i--) f.insert(a[i]);
    cout << f.cnt << endl;
    return 0;
}
```
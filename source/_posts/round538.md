---
mathjax: true
title: Codeforces Round 538 题解
tags:
  - Codeforces
  - Solution
  - DP
  - Number Theory
  - Probability
  - Bitmasks
  - SegTree
  - Data Structure
  - Interaction
categories:
  - Codeforces
date: 2019-02-11 18:07:27
---

# A Got Any Grapes?

# B Yet Another Array Partitioning Task

给一个序列，分成恰好 $k$ 个连续段，每个连续段至少有 $m$ 个，每个段的贡献是其前 $m$ 大之和，求所有段贡献之和的最大值。

所有值拖进去排序，取前 $mk$ 大之和就是答案。

构造只需要维护哪些位置是前 $mk$ 大即可。

证明的话，考虑把除了前 $mk$ 大的元素都删除，他们的有无对于答案没有影响。

全场最难。

# C Trailing Loves (or L'oeufs?)

求十进制数 $n!$ 在 $b$ 进制下有多少个尾 $0$。

题目等价于求最大的 $x$，使得 $n! =k \cdot b^x$ 成立，等价于对 $b$ 质因数分解后的每一个素因子，$n!$ 中对应素因子的幂次与其幂次之比的最小值。

于是，我们需要计算出 $n!$ 对于一个素因子 $p$ 的幂次。

显然，$1$ 到 $n$ 中 $p$ 的倍数有 $n \over p$ 个，$p^2$ 的倍数有 $n \over p^2$ 个，又由于 $p^2$ 的倍数在前面一次已经计算过一个了，只需要加上 $n \over p^2$ 即可。以此类推，于是有

$$
\alpha=\sum {n \over p^i}
$$

注意别爆掉 $long long$。

# D Flood Fill

给一个序列，一串连续的相同数字视作一个联通块，你需要先选择一个联通块，然后每次将这个联通块颜色变成另外一个以此扩大一开始的联通块，直到所有颜色都相同，求最小操作次数。

考虑 $dp[l][r][0/1]$，表示将 $[l,r]$ 区间全部变成 $l$ 处或者 $r$ 处的颜色所需要的最小操作次数。

因为只有一个起点，每次操作只需要考虑扩大一个位置即可，即 $dp[l][r][0/1]$ 只会从 $dp[l+1][r][0/1]$ 和 $dp[l][r-1][0/1]$ 转移过来。

不妨令第三个维度为 $0$，只有三种情况：

1. $[l+1,r]$ 全部变成 $l+1$ 处的颜色，如果 $l+1$ 和 $l$ 处颜色不同，那么需要多操作一次；

2. $[l+1,r]$ 全部变成 $r$ 处颜色，也是类似；

3. $[l,r-1]$ 全部变成 $l$ 处颜色，如果 $l$ 和 $r$ 处颜色不同，我们需要一次操作将 $[l,r-1]$ 先变成 $r$ 处的颜色，再一次操作变回 $l$ 处的颜色。

第三种情况考虑错了 T^T。

# E Arithmetic Progression

交互题，给定长度 $n$，$60$ 次询问，猜一个打乱过位置的等差数列的首项和公差，有两种询问方式：

1. `? i`：询问位置 $i$ 上的数字；

2. `> x`：询问是否有大于 $x$ 的数字。

先用 $30$ 次询问二分出最大值，然后随机取 $30$ 个位置，做差后全部 $gcd$ 起来就是公差。

随机别用 `rand()`， 用 `mt19937`。

# F Please, another Queries on Array?

给一个序列 $a$，其中 $1 \le a_i \le 300$，维护区间乘，询问区间乘的欧拉函数。

线段树裸题（误。

由于小于 $300$ 的素数只有 $62$ 个，欧拉函数算的时候只和乘积后的值以及这个值有哪些素因子有关。

因此只需要用 `bitset` 维护有哪些素因子，线段树维护乘法即可。

<!--more-->

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 100000 + 5;

int x, y, z, a, b, c;

int main() {
    cin >> x >> y >> z >> a >> b >> c;
    if (x > a) return puts("NO"), 0;
    a -= x;
    if (y > a + b) return puts("NO"), 0;
    if (z > a + b - y + c) return puts("NO"), 0;
    puts("YES");
    return 0;
}
```

## B

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
const int maxn = 200000 + 5;

int n, m, k, a[maxn], vis[maxn];
vector<PII> v;

int main() {
    scanf("%d%d%d", &n, &m, &k);
    for (int i = 1; i <= n; i++) scanf("%d", a + i), v.push_back({a[i], i});
    sort(v.begin(), v.end()); reverse(v.begin(), v.end());
    ll ans = 0;
    for (int i = 0; i < m * k; i++) ans += v[i].first, vis[v[i].second] = 1;
    printf("%I64d\n", ans);
    int c = 0, t = 0;
    for (int i = 1; i <= n; i++) {
        if (!vis[i]) continue;
        c++;
        if (c == m) {
            printf("%d ", i); c = 0; t++;
            if (t == k - 1) break;
        }
    }
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 100000 + 5;

ll n, b;
map<ll,int> mp;

int main() {
    cin >> n >> b;
    for (ll i = 2; i * i <= b; i++) {
        if (b % i) continue;
        int c = 0;
        while (b % i == 0) b /= i, c++;
        mp[i] = c;
    }
    if (b > 1) mp[b] = 1;
    ll ans = 1e18;
    for (auto& x: mp) {
        ll d = x.first;
        ll c = 0;
        for (ll i = 1; d <= n; d *= x.first) {
            c += n / d;
            if (n / d < x.first) break;
        }
        ans = min(ans, c / x.second);
        // cout << x.first << ' ' << c << endl;
    }
    cout << ans << endl;
    return 0;
}
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 5000 + 5;

int n, c[maxn], a[maxn], len;

int f[maxn][maxn][2];
int dp(int l, int r, int t) {
    if (f[l][r][t] != -1) return f[l][r][t];
    if (l == r) return 0;
    int ans = 0;
    if (t == 0) {
        ans = dp(l + 1, r, 0) + (int)(a[l] != a[l + 1]);
        ans = min(dp(l + 1, r, 1) + (int)(a[l] != a[r]), ans);
        ans = min(dp(l, r - 1, 0) + 2 * (int)(a[r - 1] != a[r]), ans);
    } else {
        ans = dp(l + 1, r, 1) + 2 * (int)(a[l] != a[l + 1]);
        ans = min(dp(l, r - 1, 1) + (int)(a[r - 1] != a[r]), ans);
        ans = min(dp(l, r - 1, 0) + (int)(a[l] != a[r]), ans);
    }
    return f[l][r][t] = ans;
}

int main() {
    ms(f, -1);
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%d", c + i);
        a[i] = c[i];
        // if (c[i] != c[i - 1]) a[++len] = c[i];
    }
    len = n;
    cout << min(dp(1, len, 0), dp(1, len, 1)) << endl;
    return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <ctime>
#include <random>
#include <assert.h>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 1000000 + 5;

int rand(){
    static int seed = 233333;
    return seed = int(seed * 482711ll % 2147483647);
}
int gcd(int a, int b){ return b ? gcd(b, a % b) : a; }

int n, a[maxn];

int main() {
    // srand(time(NULL)); srand(rand()); srand(rand()); srand(rand()); srand(rand());
    cin >> n;
    if (n <= 60) {
        vector<int> v;
        for (int i = 1; i <= n; i++) {
            cout << "? " << i << endl;
            int x; cin >> x; v.push_back(x);
        }
        sort(v.begin(), v.end());
        cout << "! " << v[0] << ' ' << (v.back() - v.front()) / (n - 1) << endl;
        return 0;
    }
    int l = 0, r = 1e9, mx = 0, c = 0;
    while (l <= r) {
        c++;
        int m = (l + r) >> 1, x;
        cout << "> " << m << endl; cin >> x;
        if (x == 0) r = m - 1, mx = m;
        else l = m + 1;
    }
    // cout << mx << endl;
    // int g = 0;
    for (int i = 0; i < n; i++) a[i] = i + 1;
    mt19937 rnd(time(NULL));
    shuffle(a, a + n, rnd);
    vector<int> v; 
    for (int i = 1, x; i <= 30; i++) {
        cout << "? " << a[i] << endl; cin >> x;
        v.push_back(x);
        // if (g == 0) g = mx - x;
        // else g = gcd(g, mx - x);
    }
    sort(v.begin(), v.end()); 
    int g = v[1] - v[0];
    for (int i = 1; i < v.size(); i++) {
        g = gcd(v[i] - v[i - 1], g);
        assert(v[i] != v[i - 1]);
    }

    cout << "! " << mx - g * (n - 1) << ' ' << g << endl;
    return 0;
}
```

## F

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <map>
#include <bitset>
#define assert(x) do{int a=1,b=0;if(!(x))printf("%d",a/b);}while(0)
#ifdef XLor
  #define dbg(args...) do {cout << #args << " -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 400000 + 5;

ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}

// 0~61
int pri[] = {2,3,5,7,11,13,17,19,23,29,31,37,41,43,47,53,59,61,67,71,73,79,83,89,97,101,103,107,109,113,127,131,137,139,149,151,157,163,167,173,179,181,191,193,197,199,211,223,227,229,233,239,241,251,257,263,269,271,277,281,283,293};
int inv[62];
bitset<65> fac[305];
void init() {
    for (int i = 0; i < 62; i++) inv[i] = qpow(pri[i], mod - 2);
    for (int i = 2; i <= 300; i++) {
        for (int j = 0; j < 62; j++) {
            if (i % pri[j] == 0) fac[i].set(j);
        }
    }
}

int n, q, a[maxn];

ll tr[maxn << 2], tag[maxn << 2]; bitset<65> hp[maxn << 2], ptag[maxn << 2], tot;
void pushup(int rt) {
    tr[rt] = tr[rt << 1] * tr[rt << 1 | 1] % mod;
    hp[rt] = hp[rt << 1] | hp[rt << 1 | 1];
}
void pushdown(int rt, int ln, int rn) {
    if (tag[rt] == 1) return;
    ll t = tag[rt], a = qpow(t, ln), b = qpow(t, rn);
    tr[rt << 1] = tr[rt << 1] * a % mod;
    tr[rt << 1 | 1] = tr[rt << 1 | 1] * b % mod;
    tag[rt << 1] = tag[rt << 1] * t % mod;
    tag[rt << 1 | 1] = tag[rt << 1 | 1] * t % mod;
    hp[rt << 1] |= ptag[rt];
    hp[rt << 1 | 1] |= ptag[rt];
    ptag[rt << 1] |= ptag[rt];
    ptag[rt << 1 | 1] |= ptag[rt];
    ptag[rt].reset();
    tag[rt] = 1;
}
void build(int l, int r, int rt) {
    tag[rt] = 1;
    if (l == r) {
        tr[rt] = a[l]; hp[rt] = fac[a[l]];
        return ;
    }
    int m = (l + r) >> 1;
    build(lson); build(rson);
    pushup(rt);
}
void update(int L, int R, int x, int l, int r, int rt) {
    if (L <= l && r <= R) {
        tr[rt] = tr[rt] * qpow(x, r - l + 1) % mod;
        // dbg(l, r, tr[rt]);
        tag[rt] = tag[rt] * x % mod;
        hp[rt] |= fac[x]; ptag[rt] |= fac[x];
        return ;
    }
    int m = (l + r) >> 1; pushdown(rt, m - l + 1, r - m);
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    pushup(rt);
}
ll query(int L, int R, int l, int r, int rt) {
    if (L <= l && r <= R) return tot |= hp[rt], tr[rt];
    int m = (l + r) >> 1; pushdown(rt, m - l + 1, r - m);
    ll ans = 1;
    if (L <= m) ans = query(L, R, lson);
    if (R > m) ans = ans * query(L, R, rson) % mod;
    return ans;
}

int main() {
    init();
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    build(1, n, 1);
    char op[10]; int l, r, x;
    while (q--) {
        scanf("%s%d%d", op, &l, &r);
        if (op[0] == 'M') {
            scanf("%d", &x); if (x == 1) continue;
            update(l, r, x, 1, n, 1);
        }
        if (op[0] == 'T') {
            tot.reset();
            ll ans = query(l, r, 1, n, 1);
            // cout << ans << endl << tot << endl;;
            for (int i = 0; i < 62; i++) {
                if (tot[i]) ans = ans * inv[i] % mod * (pri[i] - 1) % mod;
            }
            printf("%I64d\n", ans);
        }
    }
    return 0;
}
```
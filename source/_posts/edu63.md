---
mathjax: true
title: Educational Round 63 题解
tags:
  - Codeforces
  - Solution
  - DP
  - Math
categories:
  - Codeforces
  - Educational Round
date: 2019-05-02 20:12:28
---

# A Reverse a Substring

# B Game with Telephone Numbers

# C Alarm Clocks Everywhere

# D Beautiful Array

给定一个序列，允许将一个连续区间内所有数乘 $x$，求最大连续子段和。

考虑 $dp[i][3]$，前 $i$ 个位置的答案，最后一次用 $x$ 的最大值以及从来没用过 $x$ 的最大值。

# E Guess the Root

猜一个 $10$ 次多项式在模意义下的根，最多询问 $50$ 次多项式在某点的取值。

$11$ 次询问插出多项式，暴力枚举即可。

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
#include <string>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n; string s;

int main() {
    cin >> n >> s;
    int flag = 0;
    for (int i = 1; i < n; i++) {
        if (s[i] < s[i - 1]) {
            flag = i; break;
        }
    }
    if (!flag) puts("NO");
    else {
        puts("YES");
        printf("%d %d", flag, flag + 1);
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
#include <string>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n; char s[maxn];

int main() {
    cin >> n >> s;
    int t = n - 11, c = 0;
    string v;
    for (int i = 0; i < n; i++) {
        if (c < t / 2 && s[i] == '8') {
            c++;
        } else v.push_back(s[i]);
    }
    // cout << v << endl;
    for (int i = 0; i <= t / 2 + t % 2; i++) {
        if (v[i] == '8') return puts("YES"), 0;
    }
    puts("NO");
    // for (int i = 0; i < t; i++) {
    //     if (s[i] == '8') {
    //         c++;
    //     }
    // }
    // if (c >= t / 2 + t % 2) puts("YES");
    // else puts("NO");
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
const int maxn = 300000 + 5;

ll gcd(ll a, ll b) { return b ? gcd(b, a % b) : a; }

int n, m;
ll x[maxn];

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%I64d", x + i);
    ll g = x[2] - x[1];
    for (int i = 3; i <= n; i++) {
        g = gcd(g, x[i] - x[i - 1]);
    }
    for (int i = 1; i <= m; i++) {
        ll p; scanf("%I64d", &p);
        if (g % p == 0) {
            puts("YES");
            printf("%I64d %d", x[1], i);
            return 0;
        }
    }
    puts("NO");
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
const int maxn = 300000 + 5;

int n, x;
ll a[maxn], dp[maxn][3];

ll max(ll a, ll b, ll c) {
    return max(a, max(b, c));
}

int main() {
    scanf("%d%d", &n, &x);
    for (int i = 1; i <= n; i++) scanf("%I64d", a + i);
    ll mx = 0;
    for (int i = n; i >= 1; i--) {
        dp[i][0] = max(a[i], a[i] + max(dp[i + 1][0], dp[i + 1][1], dp[i + 1][2]));
        dp[i][1] = max(1ll * x * a[i], 1ll * x * a[i] + max(dp[i + 1][1], dp[i + 1][2]));
        // dp[i][1] = max(1ll)
        dp[i][2] = max(a[i], a[i] + dp[i + 1][2]);
        dbg(dp[i][0], dp[i][1], dp[i][2]);
        mx = max(dp[i][0], mx);
        mx = max(dp[i][1], mx);
        mx = max(dp[i][2], mx);
    }
    cout << mx << endl;
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
#include <random>
#include <ctime>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 1e6 + 3;
const int inf = 1 << 30;
const int maxn = 2000000 + 5;
const double eps = 1e-8;

int query(int x) {
    cout << "? " << x << endl;
    cin >> x; return x;
}

ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}
ll Inv(ll x) {
    return qpow(x, mod - 2);
}

ll fac[maxn], a[20], inv[maxn];
struct Lagrange {
    // 1-based
    ll cc[20]; int n;
    void build(int nn) {
        n = nn;
        for (int i = 1; i <= n; i++) {
            cc[i] = Inv(fac[i - 1] * fac[n - i]) * a[i] % mod;
            if ((n - i) % 2 == 1) cc[i] = (mod - cc[i]) % mod;
        }
    }
    ll get(ll x) {
        if (x <= n) return a[x];
        ll ans = 0, tmp = 1;
        for (int i = 1; i <= n; i++) tmp = tmp * (x - i) % mod;
        for (int i = 1; i <= n; i++) {
            ans += tmp * inv[x - i] % mod * cc[i] % mod;
            if (ans >= mod) ans -= mod;
        }
        return ans;
    }
} f;

int main() {
    fac[0] = 1; 
    for (int i = 1; i < maxn; i++) fac[i] = fac[i - 1] * i % mod, inv[i] = Inv(i);
    for (int i = 0; i <= 15; i++) {
        a[i + 1] = query(i);
    }
    f.build(13);
    for (int i = 0; i < mod; i++) {
        if (!f.get(i + 1)) {
            cout << "! " << i << endl;
            return 0;
        }
    }
    cout << "! -1" << endl;
    return 0;
}
```
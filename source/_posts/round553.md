---
mathjax: true
title: Codeforces Round 553 题解
tags:
  - Codeforces
  - Solution
  - Math
  - DP
categories:
  - Codeforces
date: 2019-04-19 17:22:20
---

真 nm 自闭（呲牙。

# A Maxim and Biology

# B Dima and a Bad XOR

给定一个 $n \times m$ 的矩阵，每一行选一个，构造一种异或和不为 $0$ 的情况。

枚举每个二进制位，统计有多少个位置必选 $1$，其他位置可选 $1$ 的，选上使得 $1$ 的总数为奇数。

# C Problem for Nazar

# D Stas and the Queue at the Buffet

给定 $n$ 对数 $(a_i,b_i)$，为这些数字重新安排位置，假设 $i$ 在位置 $j$ 上，他的权值是 $(j-1)\cdot a_i + (n-j) \cdot a_i$，求最小权值和。

先假设所有每个位置都选上了 $a_i$，只要在选 $b_i$ 时把 $a_i$ 的影响消除即可。

注意到越小的数系数应该越小，对 $b_i-a_i$ 排序，乘上贡献即可。

# E Number of Components

给定 $n$ 个在 $[1,n]$ 范围内的数，记 $f(l,r)$ 表示，选出所有范围在 $[l,r]$ 内的数，剩下的数组成了多少条线段。求 $\sum_{l=1}^n \sum_{r=l}^n f(l,r)$。

考虑每个点作为一个线段左端点的贡献。

如果 $a_i \ge a_{i-1}$，那么左端点的范围是 $[a_{i-1}+1,a_i]$，右端点的范围是 $[a_i,n]$，否则类似的计算贡献。

# F Sonya and Informatics

给定一个 $01$ 序列，做 $k$ 次操作，每次等概率选一对数交换，求最后排成不减序列的概率。

显然，顺序不影响，只要前面都是 $0$，后面都是 $1$ 即可，设有 $m$ 个 $0$，考虑 $dp[i]$ 表示前面 $m$ 个有 $i$ 个 $0$。

于是有转移方程

$$
dp[i] = dp[i] \cdot ({m \choose 2} + {n-m \choose 2}+(m-i) \cdot (n-2m+2i)) / {n \choose 2} \\
+ dp[i-1] \cdot (m-i+1)^2 / {n \choose 2} \\
+ dp[i+1] \cdot (i+1)(n-2m+i+1) / {n \choose 2}
$$

状态数只有 $100$，转移次数很大，注意到转移矩阵是固定的，矩阵快速幂即可。

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

int n; char s[maxn];

int main() {
    cin >> n >> s;
    int ans = 1e9;
    for (int i = 0; i + 3 < n ; i++) {
        int x1 = min(min(abs(s[i] - 'A'), abs(26 + 'A' - s[i])), abs(26 + s[i] - 'A'));
        int x2 = min(min(abs(s[i + 1] - 'C'), abs(26 + 'C' - s[i + 1])), abs(26 + s[i + 1] - 'C'));
        int y1 = min(min(abs(s[i + 2] - 'T'), abs(26 + 'T' - s[i + 2])), abs(26 + s[i + 2] - 'T'));
        int y2 = min(min(abs(s[i + 3] - 'G'), abs(26 + 'G' - s[i + 3])), abs(26 + s[i + 3] - 'G'));
        dbg(x1, x2, y1, y2, abs('A' + 26 - s[i]));
        ans = min(ans, x1 + x2 + y1 + y2);
    }
    cout << ans;
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
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 500 + 5;

int n, m, a[maxn][maxn], c[maxn][2], p[maxn][2];

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++) cin >> a[i][j];
    for (int t = 0; t <= 10; t++) {
        int x = 0, y = 0, z = 0;
        vector<int> ans(n + 1, 0);
        ms(c, 0); ms(p, 0);
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                // dbg(t, a[i][j] >> t & 1);
                c[i][a[i][j] >> t & 1]++;
                p[i][a[i][j] >> t & 1] = j;
            }
            if (c[i][1] && !c[i][0]) x++, ans[i] = p[i][1];
        }
        for (int i = 1; i <= n; i++) {
            if (ans[i]) continue;
            if (c[i][1] && (x % 2 == 0)) {
                ans[i] = p[i][1]; x++;
            } else ans[i] = p[i][0];
            dbg(c[i][1]);
        }
        if (x % 2 == 1) {
            dbg(t, x);
            puts("TAK");
            for (int i = 1; i <= n; i++) printf("%d ", ans[i]);
            return 0;
        }
    }
    puts("NIE");
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
const int maxn = 100000 + 5;

void add(ll& x, ll y) {
    x += y; if (x >= y) x -= mod;
}

ll cal(ll x) {
    ll a = 0, b = 0, ans = 0;
    for (int i = 0; i <= 62; i++) {
        ll tot = 1ll << i;
        if (x >= tot) {
            x -= tot;
            if (i % 2) a += tot;
            else b += tot;
        } else {
            if (i % 2) a += x;
            else b += x;
            break;
        }
    }
    // cout << a << ' ' << b;
    a %= mod; b %= mod;
    return (b * b % mod + a * (1 + a) % mod) % mod;
}

ll l, r;

int main() {
    cin >> l >> r;
    // cout << cal(r);
    cout << (cal(r) - cal(l - 1) + mod) % mod;
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
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, a[maxn], b[maxn];

int main() {
    scanf("%d", &n);
    ll ans = 0;
    for (int i = 1; i <= n; i++) {
        scanf("%d%d", a + i, b + i);
        ans += 1ll * (n - 1) * a[i]; b[i] -= a[i];
    }
    sort(b + 1, b + 1 + n); reverse(b + 1, b + 1 + n);
    for (int i = 1; i <= n; i++) {
        ans += 1ll * (i - 1) * b[i];
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
#include <map>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, a[maxn];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    ll ans = 0;
    for (int i = 1; i <= n; i++) {
        if (a[i] >= a[i - 1]) {
            ans += 1ll * (n - a[i] + 1) * (a[i] - a[i - 1]);
        } else {
            ans += 1ll * a[i] * (a[i - 1] - a[i]);
        }
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
const int maxn = 100 + 5;

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
void add(ll& x, ll y) {
    x += y; if (x >= mod) x -= mod;
}

struct Mat {
    static const int M = 100 + 5;
    ll a[M][M];
    Mat() { ms(a, 0); }
    void clear() { ms(a, 0); }
    void eye() { for (int i = 0; i < M; i++) a[i][i] = 1; }
    ll* operator [] (ll x) { return a[x]; }
    const ll* operator [] (ll x) const { return a[x]; }
    Mat operator * (const Mat& b) {
        const Mat& a = *this; Mat r;
        for (int i = 0; i < M; i++)
            for (int j = 0; j < M; j++)
                for (int k = 0; k < M; k++)
                    r[i][j] = (r[i][j] + a[i][k] * b[k][j]) % mod;
        return r;
    }
    Mat pow(ll n) const {
        Mat a = *this, r; r.eye();
        while (n > 0) {
            if (n & 1)  r = r * a;
            n >>= 1; a = a * a;
        }
        return r;
    }
    Mat operator + (const Mat& b) {
        const Mat& a = *this; Mat r;
        for (int i = 0; i < M; i++)
            for (int j = 0; j < M; j++)
                r[i][j] = (a[i][j] + b[i][j]) % mod;
        return r;
    }
    void print() const {
        for (int i = 0; i < M; i++) for (int j = 0; j < M; j++)
            printf("%lld%c", (*this)[i][j], j == M - 1 ? '\n' : ' ');
    }
} F, T;

int n, k, m, a[maxn];
ll dp[2][maxn];

int main() {
    scanf("%d%d", &n, &k);
    for (int i = 1; i <= n; i++) {
        scanf("%d", a + i);
        if (a[i] == 0) m++;
    }
    int c = 0; for (int i = 1; i <= m; i++) if (a[i] == 0) c++;
    F[c][0] = 1;
    ll iv = inv(n * (n - 1) / 2), c1 = m * (m - 1) / 2, c2 = (n - m) * (n - m - 1) / 2;
    for (int i = 0; i <= m; i++) {
        T[i][i] = 1ll * (c1 + c2 + i * (m - i) + (m - i) * (n - 2 * m + i)) * iv % mod;
        if (i > 0) {
            T[i][i - 1] = 1ll * (m - i + 1) * (m - i + 1) * iv % mod;
        }
        if (i < m) {
            T[i][i + 1] = 1ll * (i + 1) * (n - 2 * m + i + 1) * iv % mod;
        }
    }
    cout << (T.pow(k) * F)[m][0] << endl;
    // dp[0][c] = 1; int f = 0;
    
    // for (int t = 1; t <= k; t++, f ^= 1) {
    //     // ms(dp[f ^ 1], 0);
    //     dbg(t);
    //     for (int i = 0; i <= m; i++) {
    //         dp[f ^ 1][i] = dp[f][i] * (c1 + c2 + i * (m - i) + (m - i) * (n - 2 * m + i)) % mod * iv % mod;
    //         if (i > 0) {
    //             add(dp[f ^ 1][i], dp[f][i - 1] * (m - i + 1) * (m - i + 1) % mod * iv % mod);
    //         }
    //         if (i < m) {
    //             add(dp[f ^ 1][i], dp[f][i + 1] * (i + 1) * (n - 2 * m + i + 1) % mod * iv % mod);
    //         }
    //     }
    // }
    // cout << dp[f][m] << endl;
    return 0;
}
```
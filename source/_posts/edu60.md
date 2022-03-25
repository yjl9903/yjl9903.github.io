---
mathjax: true
title: Educational Round 60 题解
tags:
  - Codeforces
  - Solution
  - Binary Search
  - Interaction
categories:
  - Codeforces
  - Educational Round
date: 2019-02-19 22:27:19
---

# A Best Subsegment

给一个序列，找平均值最大且最长的连续区间的长度。

差点没反应过来？平均值最大一定是序列的最大值，就是找最长的连续最大值。

# B Emotes

# C Magic Ship

给一个长度为 $n$ 的 `UDLR` 风向序列周期出现，每天你会被风向吹一格，你自己可以移动一格，两者独立，求从起点走到终点最少需要多少天。

二分天数。当天数大于最小值时，显然可以反方向走风向，也可以到达终点，因此可以二分。

预处理出，周期序列上每天的风向偏移量，可以得到 $mid$ 天后总共偏移了多少，最后判断一下曼哈顿距离即可。

# D Magic Gems

求长度为 $n$ 的满足条件的不同 01 串个数，0 必须是连续的 $m$ 个位置。

考虑 $dp[i]$ 表示长度为 $i$ 的答案，有转移方程 $dp[i]=dp[i-1]+dp[i-m]$。

由于 $n$ 很大，$m$ 很小，矩阵快速幂加速 $dp$ 转移即可。

# E Decypher the String

给定一个串，要你猜某个 $1$ 到 $n$ 的排列，按照这个排列做一个映射得到的加密串是什么。

你只有 $3$ 次询问，$1 \le n \le 10000$。

由于 $26^3 > 10000$，对每个位置 $26$ 进制拆分后，最多 $3$ 位数。

第一次询问，$abc...zabc...z...$，则 $a$ 相同的位置无法确定，第二次询问即是 $26$ 进制拆分的第二位，第三次询问同样。

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

int n, a[maxn];

int main() {
    scanf("%d", &n); int mx = 0;
    for (int i = 1; i <= n; i++) scanf("%d", a + i), mx = max(mx, a[i]);
    int ans = 1, i = 1;
    while (i <= n) {
        if (a[i] != mx) i++;
        else {
            int j = i;
            while (j <= n && a[j] == mx) j++;
            ans = max(ans, j - i);
            i = j;
        }
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
const int maxn = 300000 + 5;

int n, m, k, a[maxn];

int main() {
    scanf("%d%d%d", &n, &m, &k);
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    sort(a, a + n);
    int t = m / (k + 1), r = m % (k + 1);
    // cout << t << ' ' << r << endl;
    cout << 1ll * t * (1ll * k * a[n - 1] + a[n - 2]) + 1ll * r * a[n - 1] << endl;
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cmath>
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

PII st, ed, pre[maxn];
int n; char s[maxn];
ll totx, toty;

int check(ll x) {
    ll k = x / n, r = x % n;
    ll dx = k * pre[n].first + pre[r].first;
    ll dy = k * pre[n].second + pre[r].second;
    ll totx = st.first + dx, toty = st.second + dy;
    if (abs(totx - ed.first) + abs(toty - ed.second) <= x) return 1;
    else return 0;
}


int main() {
    scanf("%d%d%d%d%d%s", &st.first, &st.second, &ed.first, &ed.second, &n, s);
    int dx = 0, dy = 0;
    for (int i = 0; i < n; i++) {
        if (s[i] == 'U') dy++;
        if (s[i] == 'D') dy--;
        if (s[i] == 'L') dx--;
        if (s[i] == 'R') dx++;
        pre[i + 1] = {dx, dy};
    }
    // ll ans = -1;
    // for (int i = 0; i < n; i++) {
    //     totx = st.first + pre[i].first;
    //     toty = st.second + pre[i].second;
    //     ll l = 1, r = 1e9

    // }
    ll l = 1, r = 1e16, ans = -1;
    while (l <= r) {
        ll m = (l + r) / 2;
        if (check(m)) ans = m, r = m - 1;
        else l = m + 1; 
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
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

struct Mat {
    static const int M = 100;
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
} A, X, R;

ll n; int m;

int main() {
    cin >> n >> m;
    if (m > n) return puts("1"), 0;
    A[0][0] = A[0][m - 1] = 1;
    for (int i = 1; i < m; i++) A[i][i - 1] = 1;
    for (int i = 0; i < m; i++) X[i][0] = 1;
    cout << (A.pow(n - m + 1) * X)[0][0] << endl;
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
#include <string>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 10000 + 5;

char t[maxn];
int n, f[maxn], g[maxn];

int main() {
    scanf("%s", t); n = strlen(t);
    string s, q;
    for (int i = 0; i < n; i++) s += i % 26 + 'a';
    cout << "? " << s << endl;
    cin >> q;
    for (int i = 0; i < n; i++) {
        // f[(q[i] - 'a') % 26] = i;
        f[i] = (q[i] - 'a');
    }
    
    s.clear();
    for (int i = 0; i < n; i++) s += i / 26 % 26 + 'a';
    cout << "? " << s << endl;
    cin >> q;
    for (int i = 0; i < n; i++) {
        f[i] += 26 * (q[i] - 'a');
    }

    s.clear();
    for (int i = 0; i < n; i++) s += i / 26 / 26 % 26 + 'a';
    cout << "? " << s << endl;
    cin >> q;
    for (int i = 0; i < n; i++) {
        f[i] += 676 * (q[i] - 'a');
    }
    for (int i = 0; i < n; i++) g[f[i]] = i;
    printf("! ");
    for (int i = 0; i < n; i++) printf("%c", t[g[i]]);
    return 0;
}
```
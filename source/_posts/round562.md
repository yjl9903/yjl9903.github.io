---
mathjax: true
title: Codeforces Round 562 题解
tags:
  - Codeforces
  - Solution
  - Binary Search
  - Brute Force
  - DP
  - Bitmasks
categories:
  - Codeforces
date: 2019-05-27 18:59:22
---

# A Circle Metro

# B Pairs

# C Increasing by Modulo

给定一个序列，每次选择一些位置模 $m$ 加一，求最小操作次数使得序列不减。

显然有一个 $\mathcal{O}(nm)$ 的 dp。

考虑二分答案，贪心构造使得每个位置尽量小。

# D Good Triple

给定一个 $01$ 串，求有多少子串存在 $3$ 个不同位置间隔相同且字符相同。

注意到串长大于 $10$ 时已经不可能不满足条件，暴力筛掉错误情况即可。

# E And Reachability

给定一个序列，询问 $q$ 次，两个位置 $l$ 和 $r$，存在一条路径 $a_l,a_{i_0}, a_{i_1}, \dots , a_r$，满足相邻两个至少二进制表示下有一个共同位置是 $1$。

预处理每个位置到下一个第 $i$ 位为 $1$ 的第一个位置。倒序维护一下全局的每一个二进制位的最后出现位置，对于每个数的每个 $1$ 的二进制位，从全局的后一个转移过来。询问的时候，枚举 $r$ 的二进制即可。

时间复杂度 $\mathcal{O}(n \log^2 n + q \log n)$。

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

int n, a, b, x, y;

int check1(int p) {
    if (a < x) {
        if (a <= p && p <= x) return p - a;
        return -1;
    } else {
        if (p >= a) return p - a;
        else if (p <= x) return n - a + p;
        return -1;
    }
}
int check2(int p) {
    if (b > y) {
        if (b >= p && p >= y) return b - p;
        return -1;
    } else {
        if (p <= b) return b - p;
        else if (p >= y) return b + n - p;
        return -1;
    }
}

int main() {
    cin >> n >> a >> x >> b >> y;
    int f = 0;
    for (int i = 1; i <= n; i++) {
        int a = check1(i), b = check2(i);
        // cout << i << ": "<< a << ' ' << b << endl;
        if (a == -1 || b == -1) continue;
        if (a == b) f = 1;
    }
    if (f) puts("YES");
    else puts("NO");
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
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

struct DS {
    int bag[maxn], cnt[maxn], mx = 0;
    void insert(int x, int t) {
        cnt[bag[x]]--;
        bag[x] += t;
        cnt[bag[x]]++;
        if (bag[x] == mx + 1) mx++;
        if (cnt[mx] == 0) mx--;
    }
} f;

int n, m;
PII a[maxn];
vector<int> bag[maxn];
map<int,int> mp;

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= m; i++) {
        scanf("%d%d", &a[i].first, &a[i].second);
        if (a[i].first > a[i].second) swap(a[i].first, a[i].second);
        bag[a[i].first].push_back(i);
        f.insert(a[i].second, 1);
        if (a[i].first != a[i].second) {
            bag[a[i].second].push_back(i);
            f.insert(a[i].first, 1);
        }
    }
    int fl = 0;
    for (int i = 1; i <= n; i++) {
        for (auto& x: bag[i]) {
            f.insert(a[x].second, -1);
            if (a[x].first != a[x].second) f.insert(a[x].first, -1);
        }
        if ((int)bag[i].size() + f.mx >= m) {
            fl = 1; break;
        }
        for (auto& x: bag[i]) {
            f.insert(a[x].second, 1);
            if (a[x].first != a[x].second) f.insert(a[x].first, 1);
        }
    }
    if (fl) puts("YES");
    else puts("NO");
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
const int maxn = 300000 + 5;

int n, m, a[maxn], b[maxn];

int cal(int a, int x) {
    if (a < x) return a + m - x;
    return a - x;
}

int check(int x) {
    for (int i = 1; i <= n; i++) b[i] = a[i];
    for (int i = 1; i <= n; i++) {
        if (b[i] + x >= m && (b[i] + x) % m >= b[i - 1]) b[i] = b[i - 1];
        else if (b[i] >= b[i - 1]) ;
        else if (b[i] + x < m && b[i] + x >= b[i - 1]) b[i] = b[i - 1];
        else if (b[i] + x >= m) b[i] = b[i - 1];
        else return 0;
        // dbg(i, b[i], b[i - 1]);
    }
    return 1;
}

int dp[maxn];

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    // check(0);
    // check(1);
    int l = 0, r = m - 1, ans = 0;
    while (l <= r) {
        int m = (l + r) / 2;
        if (check(m)) ans = m, r = m - 1;
        else l = m + 1;
    }
    cout << ans;
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

char s[maxn];
int n;

int main() {
    scanf("%s", s + 1); n = strlen(s + 1);
    ll ans = 1ll * (n + 1) * n / 2;
    for (int i = 1; i <= n; i++) {
        for (int j = i; j <= n; j++) {
            int f = 0;
            for (int k = 1; j - 2 * k >= i; k++) {
                if (s[j - 2 * k] == s[j - k] && s[j - k] == s[j]) {
                    dbg(s[j - 2 * k], s[j - k], s[j]);
                    f = 1; break;
                }
            }
            if (f) break;
            ans--;
        }
    }
    cout << ans;
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
const int maxn = 300000 + 5;

int n, q, a[maxn], nxt[maxn][21], nx[21];

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = 0; i < 21; i++) nxt[n + 1][i] = nx[i] = n + 1;
    for (int i = n; i >= 1; i--) {
        for (int j = 0; j < 21; j++) nxt[i][j] = n + 1;
        for (int j = 0; j < 21; j++) if (a[i] & (1 << j)) {
            int p = nx[j];
            nx[j] = nxt[i][j] = i;
            for (int k = 0; k < 21; k++) {
                nxt[i][k] = min(nxt[i][k], nxt[p][k]);
            }
        }
    }
    int l, r;
    while (q--) {
        scanf("%d%d", &l, &r);
        int f = 0;
        for (int j = 0; j < 21; j++) if (a[r] & (1 << j)) {
            if (nxt[l][j] <= r) {
                dbg(nxt[l][j]);
                f = 1; break;
            }
        }
        if (f) puts("Shi");
        else puts("Fou");
    }
    return 0;
}
```
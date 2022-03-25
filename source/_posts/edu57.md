---
mathjax: true
title: Educational Round 57 题解
tags:
  - Codeforces
  - Solution
  - Fenwick Tree
  - Data Structure
  - Probability
categories:
  - Codeforces
  - Educational Round
date: 2018-12-29 13:40:43
---

# A Find Divisible

在 $[l,r]$ 内找到 $(x,y)$，满足 $x|y$ ，保证有解。

输出 $(l,2l)$。

# B Substring Removal

在母串中删除一个子串，使得只剩下一种字母，求方案数。

分类讨论：

1. 所有字母都相同：${n+1}\choose{2}$；
2. 第一个和最后一个字母相同：(第一个字母连续区间长度+1)$\times$ (最后一个字母的连续区间长度+1)；
3. 第一个和最后一个字母不同：第一个字母连续区间长度+最后一个字母连续区间长度+1。

# C Polygon for the Angle

在正 $n$ 边形内取三个顶点连成大小为 $ang$ 的角。给定 $ang$，找最少正多少边形。

做出外接圆，圆周角对应的弧跨过 $k$ 条边，$ang=\frac{180k}{n}$，即找到最小的 $k$ 使得 $ang|180k$，并且 $k \le n-2$ （最多只能跨过 $n-2$ 条边）。

枚举一下 $k$ ，注意 $k$ 最大为 $360$。

# D Easy Problem

在字符串 $s$ 中删掉一些字符，使得 $s$ 中不含有子序列 "hard"，删掉第 $i$ 个字符的代价是 $a_i$ ，求最小代价。

考虑 $dp[n][4]$，第一维表示使得前 $n$ 个字符不含 'h' 的最小代价，第二维表示使得前 $n$ 个字符不含 'ha' 的最小代价，第三维表示使得前 $n$ 个字符不含 'har' 的最小代价，第四维表示使得前 $n$ 个字符不含 'hard' 的最小代价。

转移方程：

当 $s[i]=$"$hard$"$[j]$ 时，
$$
dp[i][j]=min(dp[i-1][j-1],dp[i-1][j]+a[i])
$$
否则：$dp[i][j]=dp[i-1][j]$。

# F Inversion Expectation

$1$ 到 $n$ 的排列中有一些位置未知，求逆序数的期望。

好题！

## 分析

排列中没有位置已知，那么期望是 $n(n-1)\over 4$ ，因为对排列中的任何一对数他们对逆序数贡献的期望为 $1\over 2$。

排列中所有位置已知，树状数组维护，倒着数一遍。

因此，我们只需要知道已知数和未知数之间的逆序数对期望的贡献。

记未知数有 $m$ 个，对于每个未知位置，他取到任何一个未知数的概率均为 $1 \over m$。

对于一个已知位置，其右边有 $k$ 个未知位置，那么如果这个数和后面任何一个未知位置对期望有贡献，则未知位置需要取一个小于当前数的未知数，每个位置之间是相互独立的，全部加起来即可。

对于这个已知位置的左边，也可以类似地 $O(1)$ 求出贡献。

<!--more-->

一开始，自己的做法是对于未知去和已知凑贡献，想的有点乱了 T^T。

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

ll l, r;

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%I64d%I64d", &l, &r);
        printf("%I64d %I64d\n", 1ll * l, 2ll * l);
    }
    return 0;
}
```

## B

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 200000 + 5;
const ll mod = 998244353;

int n; char s[maxn];

int main(){
    scanf("%d%s", &n, s);
    int flag = 1;
    for (int i = 0; i < n; i++) if (s[i] != s[0]) {
        flag = 0; break;
    }
    if (flag) {
        ll ans = 1ll * n * (n + 1) / 2ll;
        printf("%I64d", ans % mod);
        return 0;
    }
    ll cnt1 = 1, cnt2 = 1;
    for (int i = 0; i < n; i++) if (s[i] == s[0]) cnt1++; else break;
    for (int i = n - 1; i > 0; i--) if (s[i] == s[n - 1]) cnt2++; else break;
    // printf("%I64d", cnt1 * cnt2 % mod);
    if (s[0] == s[n - 1]) {
        printf("%I64d", cnt1 * cnt2 % mod);
    } else {
        printf("%I64d", (cnt1 + cnt2 - 1) % mod);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

int ang;

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%d", &ang);
        int ans = -1;
        for (int i = 3; i <= 1000; i++) if (i * ang % 180 == 0) {
            int k = i * ang / 180;
            if (k <= i - 2) {
                ans = i; break;
            }
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define assert(x) do{int a=1,b=0;if(!(x))printf("%d",a/b);}while(0)
#ifdef XLor
  #define dbg(args...) do {cout << "debug -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

int n, a[maxn]; char s[maxn];
ll dp[maxn][5], ct[maxn][5];

int main(){
    scanf("%d%s", &n, s + 1);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = 1; i <= n; i++) {
        for (int j = 0; j < 4; j++) {
            dp[i][j] = dp[i - 1][j];
        }
        if (s[i] == 'h') { // y
            dp[i][0] += 1ll * a[i]; 
        } else if (s[i] == 'a') { 
            dp[i][1] = min(dp[i - 1][0], dp[i - 1][1] + a[i]); 
        } else if (s[i] == 'r') { 
            dp[i][2] = min(dp[i - 1][1], dp[i - 1][2] + a[i]);
        } else if (s[i] == 'd') {
            dp[i][3] = min(dp[i - 1][2], dp[i - 1][3] + a[i]);
        }
        dbg(dp[i][0], dp[i][1], dp[i][2], dp[i][3]);
    }
    cout << dp[n][3];
    return 0;
}
```

## F

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 200000 + 5;
const ll mod = 998244353;

inline void add(ll& x, ll y) {
    x += y % mod; if (x >= mod) x-= mod;
}
inline ll qpow(ll x, ll n) {
    ll r = 1; 
    while (n) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r % mod;
}
inline ll inv(ll x) { return qpow(x, mod - 2); }

ll tree[maxn];
inline int lowbit(int x) {return x & -x;}
void update(int i) {
    while (i < maxn) tree[i]++, i += lowbit(i);
}
ll qsum(int i) {
    ll r = 0;
    while (i > 0) r += tree[i], i -= lowbit(i);
    return r;
}

int n, a[maxn], pre[maxn], vis[maxn];

int main(){
    scanf("%d", &n); 
    for (int i = 1; i <= n; i++) {
        scanf("%d", a + i);
        if (a[i] > 0) vis[a[i]] = 1;
    }
    for (int i = 1; i <= n; i++) pre[i] = pre[i - 1] + 1 - vis[i];
    ll ans = 1ll * pre[n] * (pre[n] - 1) % mod * inv(4) % mod;
    ll fm = inv(pre[n]), c = 0;
    for (int i = n; i >= 1; i--) {
        if (a[i] != -1) {
            add(ans, qsum(a[i]));
            update(a[i]);
            add(ans, c * fm % mod * pre[a[i]] % mod);
            add(ans, (pre[n] - c) * fm % mod * (pre[n] - pre[a[i]]) % mod);
        } else {
            c++;
        }
    }
    cout << ans;
    return 0;
}
```


---
mathjax: true
title: EOJ Monthly 2019.2 题解
tags:
- EOJ Monthly
- Graph
- DP
- Number Theory
- Solution
categories:
- 题解
date: 2019-02-22 23:13:03
---

# B 解题

给一个 $10^6$ 位的大数（每位数均为 $1$ 到 $9$），一次操作可以保留一段连续区间，其余位置置 $0$。

有 $q$ 次询问，每次询问一次操作后得到的最小的 $m$ 的倍数。

类似于询问区间异或等于 $0$，这里变成模 $m$ 加法，询问一段连续区间模 $m$ 为 $0$。

从低位往高位扫一遍，维护每种模数出现的最后位置，第一次出现同余即是最小的 $m$ 倍数。

# D 进制转换

询问 $[l,r]$ 区间内有多少个数在 $k$ 进制表示下有 $m$ 个尾 $0$。

转化为前缀和。

$k$ 进制下恰好有 $m$ 个尾 $0$，即是 $k^m$ 倍数而不是 $k^{m+1}$ 的倍数。

为了避免一些乱七八糟的情况，我选择交 python。

# E 中位数

给定一个有向无环图，每个点有一个权值 $a_i$，求 $1$ 到 $n$ 的路径中，路径经过点的点权的中位数的最大值。

二分答案，$check$ 能否构造出中位数为 $mid$ 的路径。

将点权大于等于 $mid$ 赋值为 $1$，否则为 $-1$，求 $1$ 到 $n$ 的 DAG 最长路，判断最长路是否非负即可。

# F 方差

给定一个序列，求大小为 $m$ 的子集中的方差最小值。

排过序后，一定是连续的 $m$ 个数，扫一遍即可。

<!--more-->

# 代码

## B

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#include <unordered_map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 1000000 + 5;

int n, m, q, suf[maxn];
char s[maxn];
int mp[(int)5e7 + 5];

int main() {
    scanf("%s%d", s, &q); n = strlen(s);
    while (q--) {
        scanf("%d", &m);
        suf[n] = 0; mp[0] = n;
        int flag = 1, tot = 1;
        for (int i = n - 1; i >= 0; i--, tot = tot * 10 % m) {
            suf[i] = (suf[i + 1] + 1ll * (s[i] - '0') * tot) % m;
            if (mp[suf[i]]) {
                printf("%d %d\n", i + 1, mp[suf[i]]);
                flag = 0; break;
            }
            mp[suf[i]] = i;
        }
        if (flag) puts("-1");
        for (int i = 0; i <= m; i++) mp[i] = 0;
    }
    return 0;
}
```

## D

```python
from math import pow

T = int(input())

def cal(x, k, m):
    return x // int(pow(k, m)) - x // int(pow(k, m + 1))

for i in range(T):
    l, r, k, m = map(int, input().split())
    print(cal(r, k, m) - cal(l - 1, k, m))
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
const int inf = 1 << 30;
const int maxn = 1000000 + 5;

vector<int> edge[maxn];
int n, m, a[maxn], b[maxn];

int vis[maxn], f[maxn];
int dp(int u) {
    if (vis[u]) return f[u];
    vis[u] = 1;
    for (int& v: edge[u]) {
        f[u] = max(f[u], b[v] + dp(v));
    }
    // dbg(u, f[u]);
    return f[u];
}
int check(int x) {
    for (int i = 1; i <= n; i++) if (a[i] >= x) b[i] = 1; else b[i] = -1;
    for (int i = 1; i <= n; i++) f[i] = -inf, vis[i] = 0; f[n] = 0;
    return b[1] + dp(1) >= 0;
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    while (m--) {
        int u, v; scanf("%d%d", &u, &v); edge[u].push_back(v);
    }
    int l = 0, r = 1e9, ans = -1;
    // check(4);
    while (l <= r) {
        int m = (l + r) >> 1;
        if (check(m)) ans = m, l = m + 1;
        else r = m - 1;
    }
    cout << ans << endl;
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
const int mod = 998244353;
const int maxn = 1000000 + 5;

int n, m, a[maxn];

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    sort(a, a + n);
    ll sum1 = 0, sum2 = 0;
    for (int i = 0; i < m; i++) sum1 += a[i], sum2 += 1ll * a[i] * a[i];
    ll ans = 1ll * m * sum2 - sum1 * sum1;
    for (int i = m; i < n; i++) {
        sum1 -= a[i - m]; sum2 -= 1ll * a[i - m] * a[i - m];
        sum1 += a[i]; sum2 += 1ll * a[i] * a[i];
        ans = min(ans, 1ll * m * sum2 - sum1 * sum1);
    }
    cout << ans << endl;
    return 0;
}
```
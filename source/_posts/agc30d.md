---
mathjax: true
title: AtCoder Grand Contest 30 D. Inversion Sum
date: 2019-03-06 17:23:06
tags:
- DP
- Probability
- Solution
categories:
- AtCoder
---

# 题面

给定一个长度为 $n$ 的序列 $a$，有 $q$ 次操作，每次操作交换 $a_x,a_y$。

现在，对于每次操作，你可以选择做或者不做，一共有 $2^q$ 种情况，求逆序对数之和。

其中 $1 \le n, q \le 3000$。

# 分析

发现 $n$ 很小，可以枚举序列的每一对。

将问题转化为概率，题目变成求逆序对数的期望。

考虑每一个数对的贡献，记 $f[i][j]$ 表示 $a_i>a_j$ 的概率，最终答案为

$$
2^q \cdot \sum_{i=1}^{n-1} \sum_{j=i+1}^{n} f[i][j]
$$

考虑每次操作 $(x,y)$ 对概率的影响，显然有一半的概率不变，即 $f[x][y]$，有一半的概率交换，即 $f[y][x]$。

除此以外，还有一部分是通过某一个位置中转，交换了 $(x,y)$，设中转位置为 $k$。

$x$ 和 $k$ 先交换，然后交换 $k$ 和 $y$，类似的得到相应概率 $dp$ 转移。

时间复杂度：$O(n^2+nq)$。

<!--more-->

## 代码

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
const int maxn = 3000 + 5;

ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}
ll inv(ll x) { return qpow(x, mod - 2); }

int n, q, a[maxn];
ll f[maxn][maxn];

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = 1; i <= n; i++) for (int j = 1; j <= n; j++) if (a[i] > a[j]) f[i][j] = 1;
    ll inv2 = inv(2);
    for (int i = 1, x, y; i <= q; i++) {
        scanf("%d%d", &x, &y);
        f[x][y] = f[y][x] = (f[x][y] + f[y][x]) * inv2 % mod;
        for (int j = 1; j <= n; j++) if (j != x && j != y) {
            f[x][j] = f[y][j] = (f[x][j] + f[y][j]) * inv2 % mod;
            f[j][x] = f[j][y] = (f[j][x] + f[j][y]) * inv2 % mod;
        }
    }
    ll ans = 0;
    for (int i = 1; i <= n; i++) for (int j = i + 1; j <= n; j++) {
        ans += f[i][j]; if (ans >= mod) ans -= mod;
    }
    cout << ans * qpow(2, q) % mod << endl;
    return 0;
}
```
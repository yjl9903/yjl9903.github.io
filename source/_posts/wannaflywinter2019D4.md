---
mathjax: true
title: CCPC-Wannafly Winter Camp Day4
date: 2019-01-23 22:34:08
tags:
categories:
- 训练
- Wannafly Camp
---

|     rank    | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |
| :---------: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|    11/64    |  6/11  |  O  |  .  |  O  |  O  |  .  |  O  |  O  |  !  |  O  |  .  |  !  |

<!--more-->

# A 夺宝奇兵

Solved by Henry.

# C 最小边覆盖

Solved by wb and Henry.

# D 欧拉回路

Solved by Henry.

# F 小小马

Solved by Henry and wb.

# G 置置置换

Solved by XLor.

求所有 $1$ 到 $n$ 的排列，满足一下的条件的方案数，对于所有的 $i$$(2 \le i \le n)$，若 $i$ 为奇数，则 $a[i-1]<a[i]$，否则 $a[i-1]>a[i]$。

考虑 $dp[i][j]$ 表示 $1$ 到 $i$ 的排列，最后一个数为 $j$ 的方案数。

不妨令 $i$ 为偶数，那么 $dp[i][j]$ 对应了 $1,2,\dots,j-1,j+1,\dots,i-1$ ，这 $i-1$ 个数可以建立一个映射到 $1,2,\dots ,i-1$，那么

$$
dp[i][j] = dp[i-1][j]+dp[i-1][j+1]+ \dots +dp[i-1][i-1]
$$

$i$ 为奇数时为前缀和。

时间复杂度为 $O(n^2)$。

# H 命命命运

Unsolved by Henry and wb.

# I 咆咆咆哮

Solved by Henry.

给出 $n$ 张牌，每张牌有两种属性 $a_i$ 和 $b_i$，你可以选择一种属性出牌，出 $a_i$ 表示召唤一个场攻为 $a_i$ 的随从，出 $b_i$ 表示每个随从攻击力加 $b_i$，现在要求最大化场攻。

显然，必须先召唤随从，然后使用咆哮。可以注意到场攻是关于随从数量的一个凸函数（不会证明），因此考虑三分。

$check$ 函数计算召唤 $x$ 个随从时的最大场攻，可以先当作所有牌都是召唤出随从，然后对 $x b_i-a_i$ 排序，选出最大的 $n-x$ 张牌，作为咆哮使用。

# K 两条路径

Unsolved by XLor.

权值线段树维护二进制位。

简单版：权值为 $2$ 的幂次，求最短路，[The Classic Problem](https://codeforces.com/problemset/problem/464/E)。

# 代码

## G

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 1000 + 5;

int n, dp[maxn][maxn], pre[maxn][maxn], suf[maxn][maxn];

int main(){
    scanf("%d", &n);
    dp[1][1] = 1; dp[2][1] = 1; dp[2][2] = 0;
    pre[1][1] = 1; pre[2][1] = 1; pre[2][2] = 1; pre[2][3] = 1;
    suf[1][1] = 1; suf[2][0] = 1; suf[2][1] = 1; suf[2][2] = 0;
    for (int i = 3; i <= n; i++) {
        if (i % 2) {
            for (int j = i; j >= 1; j--) {
                dp[i][j] = pre[i - 1][j - 1];
                suf[i][j] = (suf[i][j + 1] + dp[i][j]) % mod;
            }
            suf[i][0] = suf[i][1];
        } else { 
            for (int j = 1; j <= i; j++) {
                dp[i][j] = suf[i - 1][j];
                pre[i][j] = (pre[i][j - 1] + dp[i][j]) % mod;
            }
            pre[i][i + 1] = pre[i][i];
        }
    }
    int ans = 0;
    for (int i = 1; i <= n; i++) ans = (ans + dp[n][i]) % mod;
    cout << ans << endl;
    return 0;
}
```

## I

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
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

int n, k;
PII a[maxn];
ll sum;

bool cmp(PII a, PII b) {
    ll x = 1ll * k * a.second - a.first, y = 1ll * k * b.second - b.first;
    return x > y;
}
ll cal(int x) {
    k = x;
    nth_element(a, a + n - x, a + n, cmp);
    ll s = 0;
    for (int i = 0; i < n - x; i++) s += 1ll * k * a[i].second - a[i].first;
    return s + sum;
}

int main(){
    scanf("%d", &n);
    for (int i = 0; i < n; i++) scanf("%d%d", &a[i].first, &a[i].second), sum += a[i].first;
    int l = 0, r = n;
    while (l + 2 < r) {
        int ml = (2 * l + r) / 3, mr = (l + 2 * r) / 3;
        ll s1 = cal(ml), s2 = cal(mr);
        if (s1 < s2) l = ml;
        else r = mr;
    }
    cout << max(max(cal(l), cal(r)), max(cal((2 * r + l) / 3), cal((2 * l + r) / 3))) << endl;
    return 0;
}
```


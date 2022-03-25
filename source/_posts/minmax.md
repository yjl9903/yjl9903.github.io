---
mathjax: true
title: min-max 容斥
date: 2019-09-17 23:50:21
tags:
- Combinatorial
- Probability
categories:
- 组合数学
---

# min-max 容斥

$$
\max(S) = \sum_{T \subseteq S} (-1)^{|T| - 1} \min(T)
$$

# 证明

设容斥系数为 $f(|T|)$，则 $\max(S) = \sum_{T \subseteq S} f(|T|) \min(T)$。

考虑整体第 $k$ 小对最大值的贡献，显然当且仅当 $k=n$ 时，最小值有贡献，因此有下式：

$$
[ n = k ] =\sum_{i=0}^{n-k} { n-k \choose i } f(i+1)
$$

组合意义为对于大于最小值 $n-k$ 个数，可以任意选取。枚举选了 $i$ 个数，再加上固定下来的第 $k$ 小，共 $i+1$ 个数，因此乘上容斥系数 $f(i+1)$。

变换一下，可得 $[ n = 0 ] =\sum_{i=0}^{n} { n \choose i } f(i+1)$。

套用二项式反演，可知 $f(n+1)=\sum_{i=0}^n (-1)^{n-i} { n \choose i } [ i = 0 ] $。

因此 $f(n+1)=(-1)^n$，即 $f(n)=(-1)^{n-1}$。

<!--more-->

# HDu4436 Card Collector

有 $n$ 张卡牌，每秒有 $p_i$ 的概率抽到卡牌 $i$，求至少得到每个卡牌至少一张的期望时间。

注意到期望时间有最后一张被抽到的卡牌决定，即每张卡牌被抽到的概率的期望时间最大值。

套用 min-max 容斥，我们只需要求出子集 $T$ 的 $\min(T)$ 值即可，其对应的组合含义为抽到牌期望时间的最小值，即第一次抽到牌的期望时间。

考虑每次抽到子集 $T$ 中某张牌的概率等于

$$
P = \sum_{i \in T} p_{i}
$$

因此 $ E(X) = \sum_{i = 1}^{ \infty } i (1 - P)^{i-1}P$，即 $1 \over P$。

## 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <string>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n;
double a[maxn];

int main() {
    while (scanf("%d", &n) == 1) {
        for (int i = 0; i < n; i++) {
            scanf("%lf", a + i);
        }
        double ans = 0.0;
        for (int s = 1; s < (1 << n); s++) {
            int c = 0; double sum = 0;
            for (int i = 0; i < n; i++) {
                if (s >> i & 1) {
                    c++;
                    sum += a[i];
                }
            }
            if (c % 2) ans += 1.0 / sum;
            else ans -= 1.0 / sum;
        }
        printf("%.4lf\n", ans);
    }
    return 0;
}
```

# 「HAOI2015」按位或

初始有一个数 $0$，每秒有 $p_i$ 的概率或上整数 $i(0 \le i \le 2^n-1)$，求期望多少秒后数变成 $2^n−1$。

逐位考虑，即某一个位置被被覆盖的期望时间最大值，套用 min-max 容斥。

我们就是要求 $\min(S)={1 \over \sum_{T\cap S \neq \varnothing} p_T}$，考虑分母的补集 $1-\sum_{T \subseteq \overline {S} } p_T$，使用 SOS DP 容易计算。

## 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <string>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 1 << 21;
const double eps = 1e-6;

int n;
double a[maxn];

int main() {
    scanf("%d", &n);
    int ss = 1 << n, ok = 0;
    for (int i = 0; i < ss; i++) {
        scanf("%lf", a + i);
        if (a[i] > eps) {
            ok |= i;
        }
    }
    if (ok != ss - 1) return puts("INF"), 0;
    for (int i = 0; i < n; i++) {
        for (int s = 0; s < ss; s++) {
            if (s >> i & 1) a[s] += a[s ^ (1 << i)];
        }
    }
    double ans = 0;
    for (int s = 0; s < ss; s++) {
        double p = 1.0 - a[ss - 1 - s];
        if (p > eps) p = 1.0 / p;
        else p = 0;
        if (__builtin_popcount(s) % 2) ans += p;
        else ans -= p;
    }
    printf("%.6lf\n", ans);
    return 0;
}
```
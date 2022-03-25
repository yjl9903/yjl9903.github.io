---
mathjax: true
title: Codeforces Round 512 题解
tags:
- Codeforces
- Solution
- Number Theory
categories:
- Codeforces
date: 2018-09-24 00:36:54
---

# A In Search of an Easy Problem

模拟。

# B Vasya and Cornfield

当 $(x,y)$ 满足 $ d \le x+y \le 2n-d$ 并且 $-d \le y-x\le d$ 时，$(x,y)$ 在矩形内。

# C Vasya and Golden Ticket

直接枚举分成了几段，计算每段的值，模拟一下即可。

# D Vasya and Triangle

数论构造。

求解方程 $ x \times y = \frac{2nm}{k} $ 的整数解 $(x,y)$ 。

如果 k 模 2 为 0，令 $k = \frac k2$,  $ g=\gcd(n,k)$，则 $x=\frac ng$，$y=\frac{m}{\frac kg}$。

如果 k 模 2 为 1，令 $g=\gcd(n,k)$, 则 $x=\frac ng$, $y=\frac{m}{\frac kg}$, 注意到 $x = n$ 或者 $x < \frac n2$, 则可以令 $x = 2x$ 或 $y = 2y$。

# E Vasya and Good Sequences

对于原问题，将输入 $a_i$ 转换成一串表示该数二进制表示多少个1，实际上我们要求区间 $[l,r]$ 满足 $2|sum(a[l \dots r])$ 且 $\max(a[l \dots r]) <= \frac 12 sum(a[l \dots r])$ 的方案数。

对于 $a_i \ge 1$，则每个数至少有一位是 1，而位数最多只有60位，那么如果序列长度大于 60，则必然满足第二个条件。

因此对于每个位置，我们预处理他的后缀和为奇数和偶数的数量，那么不考虑第二个条件，左端点为 $l$ 对答案的贡献为 $cnt[i+1][sufSum_i \% 2]$，对于长度小于 60 的后缀，暴力查询即可。

<!--more-->

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

int n;

int main(){
    cin >> n; int flag = 1;
    for (int i = 0, x; i < n; i++){
        cin >> x;
        if (x == 1) flag = 0;
    }
    if (flag) puts("EASY");
    else puts("HARD");
    return 0;
}
```

##  B

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

int n, d, m;

int main(){
    cin >> n >> d >> m;
    for (int i = 0; i < m; i++){
        int x, y; cin >> x >> y;
        if (x + y <= 2 * n - d && x + y >= d && y - x >= -d && y - x <= d){
            puts("YES");
        }
        else puts("NO");
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

int n; char s[maxn];

int main(){
    cin >> n >> s;
    int sum = 0;
    for (int i = 0; i < n; i++) sum += s[i] - '0';
    for (int i = 2; i <= n; i++){
        if (sum % i) continue;
        int x = sum / i, flag = 1, tot = 0;
        for (int j = 0; j < n; j++){
            tot += s[j] - '0';
            if (tot == x){
                tot = 0;
            }
            else if (tot > x){
                flag = 0; break;
            }
        }
        if (flag){
            puts("YES"); return 0;
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
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

ll n, m, k;
ll gcd(ll a, ll b){return b ? gcd(b, a % b) : a;}

int main(){
    cin >> n >> m >> k;
    if (2 * n * m % k) return puts("NO"), 0;
    puts("YES");
    puts("0 0");
    if (k % 2 == 0){
        k /= 2;
        ll g = gcd(n, k);
        printf("%I64d 0\n0 %I64d", n / g, m / (k / g));
    }
    else {
        ll g = gcd(n, k), a = n / g, b = m / (k / g);
        if (g == 1) b *= 2;
        else a *= 2;
        printf("%I64d 0\n0 %I64d", a, b);
    }
    return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
#ifdef XLor
  #define dbg(args...) do {cout << "debug -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
using namespace std;
typedef long long ll;
const int maxn = 300000 + 5;

int n, a[maxn], suf[maxn], s[maxn][2];

int main(){
    scanf("%d", &n);
    for (int i = 0; i < n; i++){
        ll x; scanf("%I64d", &x); int &t = a[i];
        while (x){
            if (x & 1) t++;
            x >>= 1;
        }
    }
    s[n][0] = 1;
    ll ans = 0; int suf = 0;
    for (int i = n - 1; i >= 0; i--){
        int sum = 0, mx = 0;
        for (int j = i; j < n && j - i < 63; j++){
            sum += a[j]; mx = max(mx, a[j]);
            if (mx > sum - mx && sum % 2 == 0) ans--;
        }

        suf += a[i];
        ans += 1ll * s[i + 1][suf & 1];
        s[i][0] = s[i + 1][0]; s[i][1] = s[i + 1][1];
        s[i][suf & 1]++;
    }
    printf("%I64d", ans);
    return 0;
}
```


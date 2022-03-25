---
mathjax: true
title: Codeforces Round 511 题解
tags:
- Codeforces
- Solution
- Number Theory
categories:
- Codeforces
date: 2018-09-23 18:33:56
---

# A Little C Loves 3 I

对 $n$ 模 $3$ 分类。

# B Cover Points

$ans = \max(x_i+y_i)$。

# C Enlarge GCD

线性筛预处理 $1$ ~ $1.5e7$ 所有数的最小因子。

预处理 $a_i/=gcd$，因此答案就是从所有数中取出最多数，使得他们的 $\gcd$ 大于 $1$，也就是某个最小素因子出现次数最多。

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
    cin >> n;
    if (n % 3 == 0 || n % 3 == 1){
        printf("1 1 %d", n - 2);
    }
    else printf("1 2 %d", n - 3);
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
const int maxn = 1000 + 5;

int n;

int main(){
    cin >> n;
    ll ans = 0;
    for (int i = 0, x, y; i < n; i++){
        cin >> x >> y;
        ans = max(1ll * x + 1ll * y, ans);
    }
    cout << ans;
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 300000 + 5;
const int inf = 1.5e7 + 10;

namespace sieve{
    int vis[inf], prime[inf], divsor[inf], tot = 0;
    void init(){
        ms(vis, 0);
        for (int i = 2; i < inf; i++){
            if (!vis[i]) prime[tot++] = i, divsor[i] = i;
            for (int j = 0; j < tot && prime[j] * i < inf; j++){
                vis[i * prime[j]] = 1; divsor[i * prime[j]] = prime[j];
                if (i % prime[j] == 0) break;
            }
        }
    }
}

int gcd(int a, int b){return b ? gcd(b, a % b) : a;}
int n, a[maxn]; map<int,int> mp;

int main(){
    sieve::init();
    scanf("%d", &n); int g = 0;
    for (int i = 0; i < n; i++) scanf("%d", a + i), g = gcd(a[i], g);
    int one = 0;
    for (int i = 0; i < n; i++) {
        a[i] /= g; if (a[i] == 1) one++;
    }
    if (one == n){puts("-1"); return 0;}
    int ans = 0;
    for (int i = 0; i < n; i++){
        int x = a[i];
        while (x > 1){
            int t = sieve::divsor[x];
            mp[t]++; ans = max(ans, mp[t]);
            while (x % t == 0) x /= t;
        }
    }
    printf("%d", n - ans);
    return 0;
}
```


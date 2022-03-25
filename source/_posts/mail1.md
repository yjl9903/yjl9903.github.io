---
mathjax: true
title: Mail.Ru Cup 2018 Round 1 题解
tags:
- Codeforces
- Solution
categories:
- Codeforces
date: 2018-10-20 15:20:12
---

# A Elevator or Stairs?

读错题，自闭，还被人插了？

比较一下两种方法的用时，注意开门用时。

# B Appending Mex

定义每次操作为选取某些已有元素，添加他们的 $mex$ 到集合最后，判断到第几步发生错误。

每次添加的 $mex$ 值不会超过最大值+1，否则都能构造出来，记录一下最大值即可。

# C Candies Distribution

$n$ 个人排成一列，给每个人发糖，已知每个人得到的数量，前缀中有 $l_i$ 个大于它，后缀中有 $r_i$ 个大于它。

实际上，可以发现第 $i$ 个人的糖数是第 $l_i+r_i$ 大，通过 $n-(l_i+r_i)$ 构造出每个人的糖数，$O(n^2)$ 判断是否可行即可。

# D Changing Array

给出 $n$ 个 $k$ 位二进制数，允许将其中一些数取反，求最多区间 $[l,r]$ 的数量，使得区间内异或和不为 $0$。

首先，不考虑修改操作，记录每个前缀异或和 $pre_i$，如果有 $m$ 个位置异或和相同，那么这 $\frac{m(m-1)}{2}$ 个端点两两可以组成一个异或和为 $0$ 的段，时间复杂度 $O(n\log n)$。

对于修改操作，考虑一个贪心的算法，我们要尽量让每个前缀异或和的值出现次数尽量少，根据出现次数比较一下是否翻转，记录出一个 $map$ ，按照上方法进行计算。

注意一个细节，长度为 $0$ 的前缀和为 $0$，因为如果 $pre_i=pre_j$，区间 $(i,j]$ 异或和为 0。

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

int x, y, z, t1, t2, t3;

int main(){
    cin >> x >> y >> z >> t1 >> t2 >> t3;
    ll a = 1ll * abs(x - y) * t1;
    ll b = 1ll * abs(x - z) * t2 + 1ll * abs(x - y) * t2 + 3ll * t3;
    // cout << a << ' ' << b << endl;
    if (a >= b) puts("YES");
    else puts("NO");
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
const int maxn = 100000 + 5;

int n, a[maxn];

int main(){
    scanf("%d", &n); for (int i = 0; i < n; i++) scanf("%d", a + i);
    if (a[0] != 0) return puts("1"), 0;
    int m = 0;
    for (int i = 1; i < n; i++){
        if (a[i] > m + 1){
            printf("%d", i + 1); return 0;
        }
        m = max(a[i], m);
    }
    puts("-1");
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

int n, l[maxn], r[maxn], a[maxn];

int main(){
    scanf("%d", &n);
    for (int i = 0; i < n; i++) scanf("%d", l + i);
    for (int i = 0; i < n; i++) scanf("%d", r + i);
    if (l[0] != 0 || r[n - 1] != 0) return puts("NO"), 0;
    for (int i = 0; i < n; i++){
        a[i] = n - l[i] - r[i];
        if (a[i] < 1) return puts("NO"), 0;
    }
    for (int i = 0; i < n; i++){
        int s1 = 0, s2 = 0;
        for (int j = 0; j < n; j++){
            if (j < i && a[i] < a[j]) s1++;
            if (j > i && a[i] < a[j]) s2++;
        }
        if (s1 != l[i] || s2 != r[i]) return puts("NO"), 0;
    }
    puts("YES");
    for (int i = 0; i < n; i++) printf("%d ", a[i]);
    return 0;
} 
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 200000 + 5;

int n, k, a[maxn];
map<int,int> mp;

int main(){
    scanf("%d%d", &n, &k);
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    int s = 0; mp[0] = 1;
    for (int i = 0; i < n; i++){
        int p = s ^ a[i], q = s ^ ((1 << k) - 1 - a[i]);
        if (mp[p] <= mp[q]) mp[s = p]++;
        else mp[s = q]++;
    }
    ll ans = 1ll * n * (n + 1) / 2;
    for (auto& x : mp){
        int c = x.second;
        ans -= 1ll * c * (c - 1) / 2;
    }
    printf("%I64d", ans);
    return 0;
}
```


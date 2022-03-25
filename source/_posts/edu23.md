---
mathjax: true
title: Educational Round 23 题解
tags:
- Codeforces
- Solution
- Binary Search
categories:
- Codeforces
- Educational Round
date: 2018-11-03 22:35:54
---

# A Treasure Hunt

格点 $(x_1,y_1)$ 是否能走到 $(x_2,y_2)$ ，模 $2$ 是否相等。

# B Makes And The Product

给一个序列 $a_i$，求多少对 $(i,j,k)$ 使得 $a_i \cdot a_j \cdot a_k$ 最小。

排序之后特判 $a_0=a_1=a_2$和  $a_1=a_2$，组合数数一下。

# C Really Big Numbers

求有多小于等于 $n$ 的数，其值和各位数和之差大于等于已知常数 $s$ 。

首先注意到，如果 $n$ 满足以上性质，若其个位数改为其本身一直到 9，差值不变，若发生进位，差值不增，所以 $\forall x \ge n$ 也满足上性质，因此可以二分。

# D Imbalanced Array

定义一个序列的最大值最小值之差，求一个序列的所有连续子序列的差值之和。

可以分开计算最大值和最小值，通过类似单调队列的数据结构维护出一个位置左右能够掌管的长度即可。

时间复杂度：$O(n)$。

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

int x1, y1, x2, y2, x, y;

int main(){
    cin >> x1 >> y1 >> x2 >> y2 >> x >> y;
    int dx = abs(x1 - x2), dy = abs(y1 - y2);
    if (dx % x == 0 && dy % y == 0 && dx / x % 2 == dy / y % 2) puts("YES");
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
    sort(a, a + n);
    if (a[0] == a[1] && a[1] == a[2]){
        int i = 0; while (i < n && a[i] == a[0]) i++;
        printf("%I64d\n", 1ll * i * (i - 1) * (i - 2) / 6);
    }
    else if (a[1] == a[2]){
        int i = 1; while (i < n && a[i] == a[1]) i++;
        printf("%I64d", 1ll * (i - 1) * (i - 2) / 2);
    }
    else{
        int i = 2; while (i < n && a[i] == a[2]) i++;
        printf("%d\n", i - 2);
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

ll n, s;

int check(ll x){
    ll a = x, sum = 0;
    while (a){
        sum += a % 10; a /= 10;
    }
    return x - sum >= s;
}

int main(){
    cin >> n >> s;
    ll l = 1, r = n, ans = n + 1;
    while (l <= r){
        ll m = (l + r) / 2;
        if (check(m)) ans = m, r = m - 1;
        else l = m + 1;
    }
    cout << n - ans + 1 << endl;
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
const int maxn = 1000000 + 5;

int n, a[maxn], que[maxn], id[maxn], lt[maxn], rt[maxn];

ll cal1(){
    int r = 0, l = 1; que[0] = 1 << 30; id[0] = 0;
    for (int i = 1; i <= n; i++){
        while (1 <= r && que[r] <= a[i]) r--;
        lt[i] = i - id[r] - 1;
        que[++r] = a[i]; id[r] = i;
    }
    l = n + 1; r = n; que[n + 1] = 1 << 30; id[n + 1] = n + 1;
    for (int i = n; i >= 1; i--){
        while (l <= n && que[l] < a[i]) l++;
        rt[i] = id[l] - i - 1;
        que[--l] = a[i]; id[l] = i;
    }
    ll ans = 0;
    // for (int i = 1; i <= n; i++) {
    //     cout << lt[i] << ' ' << rt[i] << endl;
    // }
    for (int i = 1; i <= n; i++) ans += 1ll * a[i] * (lt[i] + 1) * (rt[i] + 1);
    return ans;
}
ll cal2(){
    int r = 0, l = 1; que[0] = 1 << 30; id[0] = 0;
    for (int i = 1; i <= n; i++){
        while (1 <= r && que[r] >= a[i]) r--;
        lt[i] = i - id[r] - 1;
        que[++r] = a[i]; id[r] = i;
    }
    l = n + 1; r = n; que[n + 1] = 1 << 30; id[n + 1] = n + 1;
    for (int i = n; i >= 1; i--){
        while (l <= n && que[l] > a[i]) l++;
        rt[i] = id[l] - i - 1;
        que[--l] = a[i]; id[l] = i;
    }
    ll ans = 0;
    // for (int i = 1; i <= n; i++) {
    //     cout << lt[i] << ' ' << rt[i] << endl;
    // }
    for (int i = 1; i <= n; i++) ans += 1ll * a[i] * (lt[i] + 1) * (rt[i] + 1);
    return ans;
}

int main(){
    scanf("%d", &n); for (int i = 1; i <= n; i++) scanf("%d", a + i);
    // cout << cal1() << endl;
    // cout << endl;
    // cout << cal2() << endl;
    cout << cal1() - cal2() << endl;
    return 0;
}
```


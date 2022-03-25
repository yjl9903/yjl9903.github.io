---
mathjax: true
title: Codeforces Round 517 题解
tags:
- Codeforces
- Solution
- Binary Search
categories:
- Codeforces
date: 2018-10-21 22:05:20
---

# A Golden Plate

随便数数。

# B Curiosity Has No Limits

给定两个长度为 $n-1$ 的序列 $a,b$，要求构造一个长度为 $n$ 序列 $t$ 满足一定条件。

显然可以注意到，对于大部分组合解实际上是唯一的，但是会存在一个情况解有多个并且实际上这种情况不会连续出现，因此考虑直接搜。

# C Cram Time

将 $\{1 \dots t\}$ 划分进两个大小分别为 $a,b$ 的集合中。

二分找到最大的 $t$ ，从大到小塞进 $a,b$ 内，显然是肯定可以全部放进去的。

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

int w, h, k;

int main(){
    cin >> w >> h >> k;
    ll ans = 0;
    for (int i = 1; i <= k; i++){
        ans += (w - 4 * (i - 1) + h - 4 * (i - 1) - 2) * 2;
    }
    cout << ans;
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

int n, a[maxn], b[maxn], t[maxn];

int dfs(int i){
    if (i == n) return 1;
    for (int k = 0; k < 4; k++){
        if ((k | t[i - 1]) == a[i - 1] && (k & t[i - 1]) == b[i - 1]){
            t[i] = k;
            if (dfs(i + 1)) return 1;
        }
    }
    return 0;
}

int main(){
    scanf("%d", &n);
    for (int i = 0; i + 1 < n; i++) scanf("%d", a + i);
    for (int i = 0; i + 1 < n; i++) scanf("%d", b + i);

    for (int s = 0; s < 4; s++){
        t[0] = s;
        if (dfs(1)){
            puts("YES");
            for (int i = 0; i < n; i++) printf("%d ", t[i]);
            return 0;
        }
    }
    
    puts("NO");
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

ll a, b, c;
vector<int> v[2];

int main(){
    cin >> a >> b;
    int l = 0, r = 2e9;
    while (l <= r){
        int m = l + r >> 1;
        ll t = 1ll * m * (m + 1) / 2;
        if (t <= a + b) l = m + 1;
        else r = m - 1;
    }
    // cout << l << ' ' << r << endl;
    for (int i = r; i >= 1; i--){
        if (a >= i){
            a -= i; v[0].push_back(i);
        }
        else {
            b -= i; v[1].push_back(i);
        }
    }
    printf("%d\n", v[0].size());
    for (auto x : v[0]) printf("%d ", x);
    printf("\n%d\n", v[1].size());
    for (auto x : v[1]) printf("%d ", x);
    return 0;
}
```


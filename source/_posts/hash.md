---
mathjax: true
title: 哈希
date: 2018-10-18 19:41:57
tags:
- String
- Hash
categories:
- 字符串
- 哈希
---

# Hash

## 模板

```c++
typedef long long ll;
typedef unsigned long long ull;
const int seed = 135;
const int maxn = 1000000 + 10;
const int p1 = 1e9 + 7, p2 = 1e9 + 9;

ull xp1[maxn], xp2[maxn], xp[maxn];
void init() {
    xp1[0] = xp2[0] = xp[0] = 1;
    for (int i = 1; i < maxn; ++i) {
        xp1[i] = xp1[i - 1] * seed % p1;
        xp2[i] = xp2[i - 1] * seed % p2;
        xp[i] = xp[i - 1] * seed;
    }
}

#define ENABLE_DOUBLE_HASH

// index start at 0
ull h[maxn], hl[maxn];
ull Hash(char* s) { 
    int length = strlen(s);
    ull res1 = 0, res2 = 0;
    h[length] = 0;  // ATTENTION!
    for (int j = length - 1; j >= 0; j--) {
    #ifdef ENABLE_DOUBLE_HASH
        res1 = (res1 * seed + s[j]) % p1;
        res2 = (res2 * seed + s[j]) % p2;
        h[j] = (res1 << 32) | res2;
    #else
        res1 = res1 * seed + s[j];
        h[j] = res1;
    #endif
    }
    return h[0];
}

// get hash of s[left...right-1]
ull get(int left, int right) {
    int len = right - left;
#ifdef ENABLE_DOUBLE_HASH
    unsigned int mask32 = ~(0u);
    ull left1 = h[left] >> 32, right1 = h[right] >> 32;
    ull left2 = h[left] & mask32, right2 = h[right] & mask32;
    return (((left1 - right1 * xp1[len] % p1 + p1) % p1) << 32) |
           (((left2 - right2 * xp2[len] % p2 + p2) % p2));
#else
    return h[left] - h[right] * xp[len];
#endif
}
```

## 注意：

1. 第 $n$ 位哈希值置 0，从 $n-1$ 开始逆序计算哈希值。

2. 获取子串 $[l,r)$ 的哈希值，注意预处理和取模细节。

3. 注意不要将字符映射到 $0$ 上。

<!--more-->

# HDu4080 Stammering Aliens

二分子串长度，判断是否有对应长度的子串出现超过 $m$ 次即可。

## 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef unsigned long long ull;
const int maxn = 100000 + 5;
const int seed = 135;
const int p1 = 1e9 + 7;

char s[maxn]; int n, m, ans, ansp;

ull h[maxn], xp[maxn] = {1};
void Hash(){
    ull ans = 0; h[n] = 0;
    for (int i = n - 1; i >= 0; i--) ans = (ans * seed + s[i]) % p1, h[i] = ans;
}
ull cal(int l, int r){
    int len = r - l;
    return (h[l] - h[r] * xp[len] % p1 + p1) % p1; 
}
int check(int x){
    map<ull,int> mp; int p = -1;
    for (int i = 0; i + x <= n; i++){
        ull t = cal(i, i + x); mp[t]++;
        if (mp[t] >= m) {
            p = i;
        }
    }
    return p;
}

int main(){
    for (int i = 1; i < maxn; i++) xp[i] = xp[i - 1] * seed % p1; 
    while (~scanf("%d%s", &m, s)){
        if (!m) return 0;
        n = strlen(s); Hash();
        int l = 1, r = n; ans = -1;
        while (l <= r){
            int m = l + r >> 1, flag = check(m);
            if (flag != -1) ans = m, ansp = flag, l = m + 1;
            else r = m - 1;
        }
        if (ans == -1) puts("none");
        else printf("%d %d\n", ans, ansp);
    }
    return 0;
}
```
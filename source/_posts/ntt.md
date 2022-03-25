---
mathjax: true
title: 快速数论变换
date: 2019-01-25 23:09:54
tags:
- FFT
- Math
- Number Theory
categories:
- 数学
---

# 模板

```c++
ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        x = x * x % mod; n >>= 1;
    }
    return r;
}
ll inv(ll x) { return qpow(x, mod - 2); }
ll rev[maxn << 2];
int init(int m) {
    int step = 0, n = 1;
    for (; n < m; n <<= 1) ++step;
    for (int i = 1; i < n; i++) 
        rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (step - 1));
    return n;  
}
void ntt(vector<ll>& a, int n, int op) {
    for (int i = 0; i < n; i++) if (i < rev[i]) swap(a[i], a[rev[i]]);
    for (int h = 2; h <= n; h <<= 1) {
        ll wn = qpow(3, (mod - 1) / h);
        if (op == -1) wn = inv(wn);
        for (int i = 0; i < n; i += h) {
            ll w = 1;
            for (int j = i; j < i + h / 2; j++) {
                ll u = a[j], t = a[j + h / 2] * w % mod;
                a[j] = (u + t) % mod;
                a[j + h / 2] = (u - t + mod) % mod;
                w = w * wn % mod;
            }
        }
    }
    if (op == -1) {
        ll rn = inv(n);
        for (int i = 0; i < n; i++) a[i] = a[i] * rn % mod;
    }
}
```

<!--more-->
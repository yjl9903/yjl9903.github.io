---
mathjax: true
title: 拉格朗日插值
date: 2019-05-03 17:09:28
tags:
- Math
categories:
- 数学
---

# 模板

```c++
ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}
ll Inv(ll x) {
    return qpow(x, mod - 2);
}

ll fac[maxn], a[20], inv[maxn];
struct Lagrange {
    // 1-based
    ll cc[20]; int n;
    void build(int nn) {
        n = nn;
        for (int i = 1; i <= n; i++) {
            cc[i] = Inv(fac[i - 1] * fac[n - i]) * a[i] % mod;
            if ((n - i) % 2 == 1) cc[i] = (mod - cc[i]) % mod;
        }
    }
    ll get(ll x) {
        if (x <= n) return a[x];
        ll ans = 0, tmp = 1;
        for (int i = 1; i <= n; i++) tmp = tmp * (x - i) % mod;
        for (int i = 1; i <= n; i++) {
            ans += tmp * inv[x - i] % mod * cc[i] % mod;
            if (ans >= mod) ans -= mod;
        }
        return ans;
    }
} f;
```

<!--more-->
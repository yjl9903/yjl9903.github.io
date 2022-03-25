---
mathjax: true
title: 矩阵运算
date: 2019-02-19 13:37:23
tags:
- Math
categories:
- 数学
---

# 模板

```c++
struct Mat {
    static const int M = 2;
    ll a[M][M];
    Mat() { ms(a, 0); }
    void clear() { ms(a, 0); }
    void eye() { for (int i = 0; i < M; i++) a[i][i] = 1; }
    ll* operator [] (ll x) { return a[x]; }
    const ll* operator [] (ll x) const { return a[x]; }
    Mat operator * (const Mat& b) {
        const Mat& a = *this; Mat r;
        for (int i = 0; i < M; i++)
            for (int j = 0; j < M; j++)
                for (int k = 0; k < M; k++)
                    r[i][j] = (r[i][j] + a[i][k] * b[k][j]) % mod;
        return r;
    }
    Mat pow(ll n) const {
        Mat a = *this, r; r.eye();
        while (n > 0) {
            if (n & 1)  r = r * a;
            n >>= 1; a = a * a;
        }
        return r;
    }
    Mat operator + (const Mat& b) {
        const Mat& a = *this; Mat r;
        for (int i = 0; i < M; i++)
            for (int j = 0; j < M; j++)
                r[i][j] = (a[i][j] + b[i][j]) % mod;
        return r;
    }
    void print() const {
        for (int i = 0; i < M; i++) for (int j = 0; j < M; j++)
            printf("%lld%c", (*this)[i][j], j == M - 1 ? '\n' : ' ');
    }
};
```

<!--more-->
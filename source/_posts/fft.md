---
mathjax: true
title: 快速傅里叶变换
date: 2018-11-27 21:13:55
tags:
- FFT
- Math
categories:
- 数学
---

# 模板

```c++
struct complex{
    double x, y;
    complex(double a = 0, double b = 0):x(a), y(b){}
    complex operator+(const complex& b){return complex{x + b.x, y + b.y};}
    complex operator-(const complex& b){return complex{x - b.x, y - b.y};}
    complex operator*(const complex& b){return complex{x * b.x - y * b.y, x * b.y + y * b.x};}
}a[maxn], b[maxn]; int rev[maxn];
void fft(int n, complex a[], int op = 1){
    for (int i = 0; i < n; i++) if (i < rev[i]) swap(a[i], a[rev[i]]);
    for (int i = 1; i < n; i <<= 1){
        complex t(cos(pi / i), op * sin(pi / i));
        for (int j = 0; j < n; j += (i << 1)){
            complex w(1, 0);
            for (int k = 0; k < i; k++, w = w * t){
                complex x = a[j + k], y = w * a[j + k + i];
                a[j + k] = x + y; a[j + k + i] = x - y;
            }
        }
    } 
    if (op == -1) for (int i = 0; i < n; i++) a[i].x /= n, a[i].y /= n;
}
void mul(int n, complex a[], int m, complex b[], int ans[]){
    int l = 0, lim = 1; while (lim <= n + m) l++, lim <<= 1; 
    for (int i = 0; i < lim; i++) rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (l - 1));
    fft(lim, a); fft(lim, b);
    for (int i = 0; i <= lim; i++) a[i] = a[i] * b[i];
    fft(lim, a, -1);
    for (int i = 0; i <= n + m; i++) ans[i] = (int)(a[i].x + 0.5);
}
```
<!--more-->
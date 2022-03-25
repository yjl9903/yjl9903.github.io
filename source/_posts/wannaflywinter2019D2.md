---
mathjax: true
title: CCPC-Wannafly Winter Camp Day2
date: 2019-01-21 22:37:49
tags:
- Geometry
categories:
- 训练
- Wannafly Camp
---

|     rank    | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |
| :---------: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|    29/99    |  2/12  |  .  |  .  |  .  |  .  |  !  |  Ø  |  .  |  O  |  .  |  .  |  !  |  .  |

自闭了。

<!--more-->

# F Quicksort

Upsolved by XLor.

随机一个 $1$ 到 $n$ 的排列，进行 $h$ 趟快速排序（感受到来自数据结构课的怨念），求逆序对数的期望。

考虑不进行快速排序，答案显然。

考虑进行一趟快速排序，枚举中间点，左边都比中间小，右边都比中间大，因此左右之间期望相互独立没有影响，逆序数仅与左右两段区间长度有关（有用只有相对大小关系，因此可以离散化）。最后除上枚举中间点的概率。

设 $dp[n][k]$ 表示长度为 $n$ 进行 $k​$ 趟的快速排序的期望，得到转移方程

$$
dp[n][h]={1 \over n} \sum_{i=1}^{n}(dp[i-1][h-1]+dp[n-i][h-1]) \\
dp[n][1]={n(n-1) \over 4}
$$

前缀和优化一下转移即可。

# H Cosmic Cleaner

Solved by XLor and Grunt.

贴个球的体积交板子即可。

[球的体积交](https://blog.csdn.net/enterprise_/article/details/81624174)。

做法是用球缺体积公式：$V=\pi h^2 (r - {h \over 3})$

# 代码

## F

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 6000 + 5;

int inv[maxn] = {0ll, 1ll}, f[maxn] = {1ll, 1ll}, dp[maxn][maxn];
int n, k;

int main(){
    for (int i = 2; i < maxn; i++) inv[i] = (mod - mod / i) * 1ll * inv[mod % i] % mod, f[i] = 1ll * i * f[i - 1] % mod;
    for (int i = 1; i < maxn; i++) dp[i][1] = (dp[i - 1][1] + 1ll * i * (i - 1) * inv[4] % mod) % mod;
    for (int h = 2; h < maxn; h++) {
        for (int i = 1; i < maxn; i++) {
            dp[i][h] = (dp[i - 1][h] + 2ll * dp[i - 1][h - 1] % mod * inv[i] % mod) % mod;
        }
    }
    int T, kase = 0; scanf("%d", &T);
    while (T--){
        scanf("%d%d", &n, &k);
        ll ans = dp[n][k] - dp[n - 1][k] + mod;
        printf("Case #%d: %lld\n", ++kase, ans * f[n] % mod);
    }
    return 0;
}
```

## H

```c++
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;
const double eps = 1e-8;
const double pi = acos(-1.0);

struct Point {
    double x, y, z;
    Point(double a = 0, double b = 0, double c = 0): x(a), y(b), z(c){}
    Point operator -(const Point &b)const {     //返回减去后的新点
        return Point(x - b.x, y - b.y,z-b.z);
    }
    Point operator +(const Point &b)const {     //返回加上后的新点
        return Point(x + b.x, y + b.y,z+b.z);
    }
    //数乘计算
    Point operator *(const double &k)const {    //返回相乘后的新点
        return Point(x * k, y * k,z*k);
    }
    Point operator /(const double &k)const {    //返回相除后的新点
        return Point(x / k, y / k,z/k);
    }
    double operator *(const Point &b)const {    //点乘
        return x*b.x + y*b.y+z*b.z;
    }
};

double dist(Point p1, Point p2) {
    return sqrt((p1 - p2)*(p1 - p2));
}
struct sphere {
    double r;
    Point centre;
} a[maxn], sp;

double SphereInterVS(sphere a, sphere b) {
    double d = dist(a.centre, b.centre);//球心距
    double t = (d*d + a.r*a.r - b.r*b.r) / (2.0 * d);//
    double h = sqrt((a.r*a.r) - (t*t)) * 2;//h1=h2，球冠的高
    double angle_a = 2 * acos((a.r*a.r + d*d - b.r*b.r) / (2.0 * a.r*d));  //余弦公式计算r1对应圆心角，弧度
    double angle_b = 2 * acos((b.r*b.r + d*d - a.r*a.r) / (2.0 * b.r*d));  //余弦公式计算r2对应圆心角，弧度
    double l1 = ((a.r*a.r - b.r*b.r) / d + d) / 2;
    double l2 = d - l1;
    double x1 = a.r - l1, x2 = b.r - l2;//分别为两个球缺的高度
    double v1 = pi*x1*x1*(a.r - x1 / 3);//相交部分r1圆所对应的球缺部分体积
    double v2 = pi*x2*x2*(b.r - x2 / 3);//相交部分r2圆所对应的球缺部分体积
    return v1 + v2;
}

int n;

int main(){
    int T, kase = 0; scanf("%d", &T);
    while (T--){
        scanf("%d", &n);
        for (int i = 1; i <= n; i++) scanf("%lf%lf%lf%lf", &a[i].centre.x, &a[i].centre.y, &a[i].centre.z, &a[i].r);
        scanf("%lf%lf%lf%lf", &sp.centre.x, &sp.centre.y, &sp.centre.z, &sp.r);
        double ans = 0;
        for (int i = 1; i <= n; i++) {
            double d1 = dist(a[i].centre, sp.centre);
            if (d1 < a[i].r + sp.r) {
                if (d1 + a[i].r < sp.r) {
                    ans += 4.0 * pi / 3.0 * a[i].r * a[i].r * a[i].r;
                } else {
                    ans += SphereInterVS(a[i], sp);
                }
            }
        }
        printf("Case #%d: %.10lf\n", ++kase, ans);
    }
    return 0;
}
```


---
mathjax: true
title: 模拟退火
date: 2018-10-30 01:29:00
tags:
- other
categories:
- 乱七八糟
---

# JSOI2004 平衡点

```c++
const double delta = 0.998;
const double eps = 1e-15;

int n, x[maxn], y[maxn], w[maxn];
double ansx, ansy, ans = 1e18;

double cal(double a, double b){
    double ans = 0;
    for (int i = 0; i < n; i++){
        double dx = a - x[i], dy = b - y[i];
        ans += sqrt(dx * dx + dy * dy) * w[i];
    }
    return ans;
}

void get(){
    double x = ansx, y = ansy, t = 2000;
    while (t > eps){
        double tx = x + ((rand() << 1) - RAND_MAX) * t;
        double ty = y + ((rand() << 1) - RAND_MAX) * t;
        double now = cal(tx, ty), d = now - ans;
        if (d < 0){
            x = tx; y = ty;
            ansx = tx; ansy = ty; ans = now;
        }
        else if (exp(-d / t) * RAND_MAX > rand()) x = tx, y = ty;
        t *= delta;
    }
}
```

<!--more-->

# 玄学建议

1. eps为终止条件，delta为降温系数。

2. 答案从均值开始跑。

3. 换个随机数种子，多跑几遍。

4. 调大降温系数delta，调大初始温度，调小终止条件。


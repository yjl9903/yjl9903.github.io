---
mathjax: true
title: 后缀数组
date: 2018-10-23 22:04:46
tags:
- String
- Suffix Array
categories:
- 字符串
- 后缀数组
---

# 模板

```c++
namespace SA {
    int n, m, sa[maxn], h[maxn], c[maxn], x[maxn], y[maxn];
    void rsort() {
        for (int i = 1; i <= m; i++) c[i] = 0;
        for (int i = 1; i <= n; i++) c[x[i]]++;
        for (int i = 1; i <= m; i++) c[i] += c[i - 1];
        for (int i = n; i >= 1; i--) sa[c[x[y[i]]]--] = y[i];
    }
    int cmp(int i, int j, int k) {
        int a = i + k > n ? -1 : y[i + k];
        int b = j + k > n ? -1 : y[j + k];
        return y[i] == y[j] && a == b;
    }
    void build(int nn, char* s) {
        n = nn; m = 255; // important
        for (int i = 1; i <= n; i++) x[i] = s[i], y[i] = i;
        rsort();
        for (int k = 1, p; k <= n; k += k, m = p) {
            p = 0;
            for (int i = n; i > n - k; i--) y[++p] = i;
            for (int i = 1; i <= n; i++) if (sa[i] > k) y[++p] = sa[i] - k;
            rsort(); 
            for (int i = 1; i <= n; i++) swap(x[i], y[i]);
            x[sa[1]] = p = 1;
            for (int i = 2; i <= n; i++) x[sa[i]] = cmp(sa[i], sa[i - 1], k) ? p : ++p;
        }
        for (int i = 1; i <= n; i++) x[sa[i]] = i;
        for (int i = 1, k = 0; i <= n; i++) {
            if (k) k--;
            int j = sa[x[i] - 1];
            while (i + k <= n && j + k <= n && s[i + k] == s[j + k]) k++;
            h[x[i]] = k;
        }
    }
}
```

<!--more-->

# LCP 模板

```c++
namespace SA {
    int n, m, sa[maxn], h[maxn], c[maxn], x[maxn], y[maxn];
    void rsort() {
        for (int i = 1; i <= m; i++) c[i] = 0;
        for (int i = 1; i <= n; i++) c[x[i]]++;
        for (int i = 1; i <= m; i++) c[i] += c[i - 1];
        for (int i = n; i >= 1; i--) sa[c[x[y[i]]]--] = y[i];
    }
    int cmp(int i, int j, int k) {
        int a = i + k > n ? -1 : y[i + k];
        int b = j + k > n ? -1 : y[j + k];
        return y[i] == y[j] && a == b;
    }
    int dp[maxn][21];
    void build(int nn, char* s) {
        n = nn; m = 255;
        for (int i = 1; i <= n; i++) x[i] = s[i], y[i] = i;
        rsort();
        for (int k = 1, p; k <= n; k += k, m = p) {
            p = 0;
            for (int i = n; i > n - k; i--) y[++p] = i;
            for (int i = 1; i <= n; i++) if (sa[i] > k) y[++p] = sa[i] - k;
            rsort(); 
            for (int i = 1; i <= n; i++) swap(x[i], y[i]);
            x[sa[1]] = p = 1;
            for (int i = 2; i <= n; i++) x[sa[i]] = cmp(sa[i], sa[i - 1], k) ? p : ++p;
        }
        for (int i = 1; i <= n; i++) x[sa[i]] = i;
        for (int i = 1, k = 0; i <= n; i++) {
            if (k) k--;
            int j = sa[x[i] - 1];
            while (i + k <= n && j + k <= n && s[i + k] == s[j + k]) k++;
            h[x[i]] = k;
        }
        for (int i = 1; i <= n; i++) dp[i][0] = h[i];
        for (int j = 1; j < 21; j++) {
            for (int i = 1; i + (1 << j) <= n + 1; i++) {
                dp[i][j] = min(dp[i][j - 1], dp[i + (1 << (j - 1))][j - 1]);
            }
        }
    }
    int qlcp(int l, int r) {
        if (l == r) return n - l + 1;
        l = x[l]; r = x[r];
        if (l > r) swap(l, r);
        l++;
        int k = 0; while ((1 << (k + 1)) <= r - l + 1) k++;
        return min(dp[l][k], dp[r - (1 << k) + 1][k]);
    }
}
```

# 注意

1. 下标从 $1$ 开始。

2. 每个字符的值域范围。

# 定义

1. $sa[n]$ : 后缀排序后，排名第 $i$ 个串的开始下标。
2. $rk[n]$ : 原串第 $i$ 个后缀排过序后的名次。
3. $sa[n]$ 和 $rk[n]$ 互为逆运算，$rk[sa[i]]=sa[rk[i]]=i$。
4. $LCP$ : 最长公共前缀。
5. $height[rk[i]] \ge height[rk[i-1]] - 1$。
6. $height[i]$ = $LCP(sa[i - 1],sa[i])$ ($2 \le i \le n$)。 

# 一个串的经典问题

## 本质不同子串个数

${n(n+1) \over 2}-\sum_{i=2}^n height[i]$。

## 可重叠的最长重复子串

$height$ 数组最大值。

## 不可重叠的最长重复子串

二分答案。

当前二分到 $mid$, 将 $height$ 数组中连续大于等于 $mid$ 的位置划分进一块，一个块内求出现位置的最大值和最小值，如果相差大于等于 $mid$，即代表出现了一段不重叠的重复子串。

```c++
int check(int len) {
    int mx = sa[1], mn = sa[1];
    for (int i = 2; i <= n; i++) {
        if (h[i] >= len) {
            mn = min(mn, sa[i]);
            mx = max(mx, sa[i]);
            if (mx - mn >= len) return 1;
        } else mx = sa[i], mn = sa[i];
    }
    return 0;
}
```

## 循环节最多的子串

枚举循环节大小 $len$，求 $s[1],s[1+len], s[1+2\cdot len], \dots$，相邻两个的 $LCP$，设 $LCP$ 为 $x$，则循环节个数至少为 ${x \over len} + 1$，再特判一下剩余的 $len - x \bmod len$ 个字符，将 $s[1+k\cdot len]$ 向前推这么多能否构成一个更多的循环节。

# 两个串的经典问题

## 最长公共子串

将两个串用一个小字符连起来，建一个后缀数组。

$ans=max(height[i])$，满足 $sa[i]$ 和 $sa[i-1]$ 不在同一个串内。

# 多个串的经典问题

将多个串用不同的未出现字符连成一个（注意值域大小）。

## 出现在至少 $k$ 个串中的最长子串

二分答案，对 $height$ 分块，判断一个块内是否出现 $k$ 次。
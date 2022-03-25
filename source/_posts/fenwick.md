---
mathjax: true
title: 树状数组上的黑科技
date: 2019-06-18 00:33:59
tags:
- Data Structure
- Fenwick Tree
categories:
- 数据结构
- 树状数组
---

参考博客 [树状数组黑科技讲义](https://magolor.github.io/2018/08/27/2018-08-27-blog-01/)。

# 区间加 区间询问

令 $b_i=a_i-a_{i-1}$，那么 $a_x=\sum_{i=1}^x b_i$。于是，对于区间询问有

$$
\sum_{i=l}^r a_i = (r-l+1)\sum_{i=1}^{l-1} b_i + (r+1) \sum_{i=l}^r b_i - \sum_{i=l}^r i \cdot b_i
$$

分别在树状数组数组上维护 $b_i$ 和 $i \cdot b_i$ 即可。

# 权值树状数组二分第 $k$ 大

树状数组对应一棵没有右儿子的权值线段树，权值线段树上二分时，只需要和左儿子比较，因此权值线段树上二分可以扩展到树状数组上。

更新时，正常单点更新即可；二分时，每个点的权值对应线段树上结点的权值。

树状数组上结点 $p$ 的左儿子为 $p - lowbit(p) / 2$，右儿子为 $p + lowbit(p) / 2$。

注意边界上的细节，数组大小建议放大到 $2$ 的幂次。

<!--more-->

# 代码

## 区间加 区间询问

```c++
int c[maxn], s[maxn];
inline int lowbit(int i) { return i & -i; }
inline void update(int i, int x) {
    int y = i * x;
    for (; i < maxn; i += lowbit(i)) {
        c[i] += x; s[i] += y;
    }
}
inline void update(int l, int r, int x) {
    update(l, x); update(r + 1, -x);
}
inline int query(int* a, int i) {
    int r = 0;
    for (; i; i -= lowbit(i)) r += a[i];
    return r;
}
inline int query(int l, int r) {
    return (r - l + 1) * query(c, l - 1) + (r + 1) * (query(c, r) - query(c, l - 1)) 
            - query(s, r) + query(s, l - 1);
}
```

## 权值树状数组二分第 $k$ 大

```c++
struct BIT {
    static const int R = 1 << 21;
    int a[R];
    inline int lowbit(int x) { return x & -x; }
    void insert(int i) {
        for (; i < R; i += lowbit(i)) a[i]++;
    }
    int findx(int p, int rk) {
        // if (p > R) return -1;
        if (p & 1) {
            // if (p + (a[p] < rk) > R) return -1;
            return p + (a[p] < rk);
        } else {
            if (rk > a[p]) return findx(p + lowbit(p) / 2, rk - a[p]);
            else return findx(p - lowbit(p) / 2, rk);
        }
    }
    int findx(int rk) {
        return findx(R >> 1, rk);
    }
} f;
```
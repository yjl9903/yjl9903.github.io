---
title: 线段树
date: 2018-08-07 20:57:33
tags: 
- Data Structure
- SegTree
categories: 
- 数据结构 
- 线段树
comments: false
---

# 线段树模板

{% codeblock lang:cpp %}
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
const int maxn = 100000 + 5;

// sum 节点, add 懒惰， a 原数组
int sum[maxn << 2], add[maxn << 2], a[maxn], n;
void pushup(int rt){sum[rt] = sum[rt << 1] + sum[rt << 1 | 1];}
void pushdown(int rt, int ln, int rn){
    if (add[rt]){
        add[rt << 1] += add[rt]; add[rt << 1 | 1] += add[rt];
        sum[rt << 1] = add[rt] * ln; sum[rt << 1 | 1] = add[rt] * rn;
        add[rt] = 0;
    }
}
void build(int l, int r, int rt){
    if (l == r){
        sum[rt] = a[l];
        return;
    }
    int m = (l + r) >> 1;
    build(lson); build(rson);
    pushup(rt);
}
void update(int i, int x, int l, int r, int rt){
    if (l == r){
        sum[rt] += x;
        return;
    }
    int m = (l + r) >> 1;
    if (i <= m) update(i, x, lson);
    else update(i, x, rson);
    pushup(rt);
}
void update(int L, int R, int x, int l, int r, int rt){
    if (L <= l && r <= R){
        sum[rt] += x * (r - l + 1);
        add[rt] += x;
        return;
    }
    int m = (l + r) >> 1;
    pushdown(rt, m - l + 1, r - m);
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    pushup(rt);
}
int query(int i, int l, int r, int rt){
    if (l == r){
        return sum[rt];
    }
    int m = (l + r) >> 1;
    if (i <= l) return query(i, lson);
    return query(i, rson);
}
int query(int L, int R, int l, int r, int rt){
    if (L <= l && r <= R){
        return sum[rt];
    }
    int m = (l + r) >> 1;
    pushdown(rt, m - l + 1, r - m);
    int ans = 0;
    if (L <= m) ans += query(L, R, lson);
    if (R > m) ans += query(L, R, rson);
    return ans;
}
{% endcodeblock %}

# 区间加乘双标记

```c++
int n, m, p;
ll a[maxn], sum[maxn << 2], add[maxn << 2], mul[maxn << 2];

void pushup(int rt){ sum[rt] = (sum[rt << 1] + sum[rt << 1 | 1]) % p; }
void pushdown(int rt, int ln, int rn){
    int ls = rt << 1, rs = rt << 1 | 1;
    if (mul[rt] != 1){
        mul[ls] *= mul[rt]; mul[rs] *= mul[rt];
        add[ls] *= mul[rt]; add[rs] *= mul[rt];
        sum[ls] *= mul[rt]; sum[rs] *= mul[rt];
        mul[rt] = 1;
    }
    if (add[rt] != 0){
        add[ls] += add[rt]; add[rs] += add[rt];
        sum[ls] += add[rt] * ln; sum[rs] += add[rt] * rn;
        add[rt] = 0;
    }
}
void build(int l, int r, int rt){
    mul[rt] = 1;
    if (l == r){
        sum[rt] = a[l] % p; return;
    }
    int m = l + r >> 1;
    build(lson); build(rson);
    pushup(rt);
}
void update1(int L, int R, int x, int l, int r, int rt){
    if (L <= l && r <= R){
        sum[rt] *= x;
        mul[rt] *= x;
        add[rt] *= x;
        return;
    }
    int m = l + r >> 1; pushdown(rt, m - l + 1, r - m);
    if (L <= m) update1(L, R, x, lson);
    if (R > m) update1(L, R, x, rson);
    pushup(rt);
}
void update2(int L, int R, int x, int l, int r, int rt){
    if (L <= l && r <= R){
        sum[rt] += x * (r - l + 1);
        add[rt] += x;
        return;
    }
    int m = l + r >> 1; pushdown(rt, m - l + 1, r - m);
    if (L <= m) update2(L, R, x, lson);
    if (R > m) update2(L, R, x, rson);
    pushup(rt);
}
```

# 区间取模

```c++
int n, m, a[maxn], mx[maxn << 2];;
ll sum[maxn << 2];

void pushup(int rt){
    sum[rt] = sum[rt << 1] + sum[rt << 1 | 1];
    mx[rt] = max(mx[rt << 1], mx[rt << 1 | 1]);
}
void build(int l, int r, int rt){
    if (l == r) {
        sum[rt] = mx[rt] = a[l]; return;
    }
    int m = l + r >> 1;
    build(lson); build(rson);
    pushup(rt);
}
void update(int i, int x, int l, int r, int rt){
    if (l == r){
        sum[rt] = mx[rt] = x; return;
    }
    int m = l + r >> 1;
    if (i <= m) update(i, x, lson);
    else update(i, x, rson);
    pushup(rt);
}
void update(int L, int R, int mod, int l, int r, int rt){
    if (L > r || l > R || mx[rt] < mod) return;
    if (L <= l && r <= R && l == r){
        sum[rt] = mx[rt] = mx[rt] % mod;
        return;
    }
    int m = l + r >> 1;
    update(L, R, mod, lson); update(L, R, mod, rson);
    pushup(rt);
}
```
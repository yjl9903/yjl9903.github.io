---
mathjax: true
title: 莫队算法
date: 2019-05-22 19:59:19
tags:
- Data Structure
categories:
- 数据结构
---

# 模板

```c++
const int sz = 700;
const int maxn = 100000 + 5;

ll gcd(ll a, ll b) { return b ? gcd(b, a % b) : a; }
PII get(ll a, ll b) {
    if (a == 0) return { 0, 1 };
    ll g = gcd(a, b);
    return { a / g, b / g };
}

struct Que {
    int l, r, id;
    bool operator<(const Que& b) const {
        if (l / sz == b.l / sz) {
            if ((l / sz) % 2) return r > b.r;
            else return r < b.r;
        }
        return l < b.l;
    }
} q[maxn];

int n, m, a[maxn];
PII ans[maxn];

ll fz, bag[maxn];
ll cal(ll x) {
    return x * (x - 1) / 2;
}
void update(int x, int t) {
    fz -= cal(bag[x]);
    bag[x] += t;
    fz += cal(bag[x]);
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = 1; i <= m; i++) scanf("%d%d", &q[i].l, &q[i].r), q[i].id = i;
    sort(q + 1, q + 1 + m);
    int l = 1, r = 0; // important
    for (int i = 1; i <= m; i++) {
        while (r < q[i].r) update(a[++r], 1);
        while (l > q[i].l) update(a[--l], 1);
        while (l < q[i].l) update(a[l++], -1);
        while (r > q[i].r) update(a[r--], -1);
        dbg(q[i].l, q[i].r, l, r);
        ans[q[i].id] = get(fz, cal(r - l + 1));
    }
    for (int i = 1; i <= m; i++) {
        printf("%lld/%lld\n", ans[i].first, ans[i].second);
    }
    return 0;
}
```

<!--more-->
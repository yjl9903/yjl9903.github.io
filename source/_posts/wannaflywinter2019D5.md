---
mathjax: true
title: CCPC-Wannafly Winter Camp Day5
date: 2019-01-24 23:15:33
tags:
- FFT
- Data Structure 
categories:
- 训练
- Wannafly Camp
---

|     rank    | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |
| :---------: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|    28/56    |  3/10  |  !  |  .  |  !  |  .  |  Ø  |  .  |  !  |  .  |  Ø  |  O  |

$C$ 卡主席树内存，是不是有点毒瘤啊？不会卡内存，自闭了啊？

<!--more-->

# E Fast Kronecker Transform

Upsolved by XLor and wb.

枚举每种权值，直接上 $FFT$ 显然是 $O(n^2 \log n)$，加上一个大常数，显然不行。

因此，我们可以考虑每种权值的出现次数小的暴力，大的上 $FFT$。

设阈值为 $T$，暴力的复杂度为 $O(T^2)$，$FFT$ 的复杂度为 $O(n \log n)$。

$$
T^2 \cdot n = n \log n \cdot {n \over T} \\
T = \sqrt[3]{n ^ 2 \log n} \approx 5000
$$

总题复杂度外面还要套上一个 $O(\log n)$ 离散化。

# I Sorting

Upsolved by XLor.

给一个序列 $a_1,a_2,\dots,a_n$，分界点 $x$，维护 $3$ 个操作：

1. 区间和询问；
2. 将区间 $[l,r]$ 内的数拿出来，将小于等于 $x$ 的数按原来的顺序放回去，在把大于 $x$ 的树按原来的顺序放回去；
3. 同操作 $2$，先放大于 $x$，再放小于等于 $x$。

观察到小于等于 $x$ 的数之间相对位置不变，大于 $x$ 的数之间的相对位置也没有变。

然后我们可以分开讨论大小两种数，对于区间和询问，转为询问两种数的区间和。

我们可以将小于等于 $x$ 的数看成 $0$，大于 $x$ 的数看成 $1$，实际上 $2$ 和 $3$ 两类操作变成了区间覆盖。

询问区间和时，可以算一下左、右端点左边有多少 $1$，这样就知道了 $1$ 的范围，同理 $0$ 的范围也知道，两类数的和用前缀和预处理即可。

我根本不会写数据结构.jpg。

# J Special Judge

Solved by Henry.

# 代码

## E

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 200000 + 5;

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

map<int,vector<int> > mpa, mpb;
map<int,int> mp;
int n, m, a[maxn], b[maxn];
ll ans[maxn];

int main(){
    const int T = 10000;
    scanf("%d%d", &n, &m);
    for (int i = 0; i <= n; i++) scanf("%d", a + i), mpa[a[i]].push_back(i), mp[a[i]]++;
    for (int i = 0; i <= m; i++) scanf("%d", b + i), mpb[b[i]].push_back(i), mp[b[i]]++;
    int lim = init(n + m + 1);
    for (auto& x: mp) {
        if (x.second <= T) { // O(T^2) -> N / T
            vector<int> va = mpa[x.first], vb = mpb[x.first];
            for (int& x: va) for (int& y: vb) ans[x + y] = (ans[x + y] + 1ll * x * y % mod) % mod;
        } else { // O(n \log n) -> N / T
            vector<ll> a(lim, 0), b(lim, 0);
            vector<int> va = mpa[x.first], vb = mpb[x.first];
            for (int& x: va) a[x] = x; for (int& y: vb) b[y] = y;
            ntt(a, lim, 1); ntt(b, lim, 1);
            for (int i = 0; i < lim; i++) a[i] = a[i] * b[i] % mod;
            ntt(a, lim, -1);
            for (int i = 0; i <= n + m; i++) ans[i] = (ans[i] + a[i]) % mod;
        }
    }
    for (int i = 0; i <= n + m; i++) {
        if (i) putchar(' ');
        printf("%lld", ans[i]);
    }
    return 0;
}
```

## I

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define assert(x) do{int a=1,b=0;if(!(x))printf("%d",a/b);}while(0)
#ifdef XLor
  #define dbg(args...) do {cout << #args << " -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 200000 + 5;

int n, q, x, a[maxn];

int tree[maxn << 2], tag[maxn << 2];
void pushup(int rt) {
    tree[rt] = tree[rt << 1] + tree[rt << 1 | 1];
}
void pushdown(int rt, int ln, int rn) {
    if (tag[rt] == 0) {
        tree[rt << 1] = tree[rt << 1 | 1] = 0;
        tag[rt << 1] = tag[rt << 1 | 1] = 0; 
    } else if (tag[rt] == 1) {
        tree[rt << 1] = ln; tag[rt << 1] = 1;
        tree[rt << 1 | 1] = rn; tag[rt << 1 | 1] = 1;
    }
    tag[rt] = -1;
}
void build(int l, int r, int rt) {
    tag[rt] = -1;
    if (l == r) {
        tree[rt] = (a[l] > x);
        return;
    }
    int m = (l + r) >> 1;
    build(lson); build(rson);
    pushup(rt);
}
void update(int L, int R, int x, int l, int r, int rt) {
    if (L <= l && r <= R) {
        tree[rt] = (r - l + 1) * x; tag[rt] = x; return;
    }
    int m = (l + r) >> 1; pushdown(rt, m - l + 1, r - m);
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    pushup(rt);
}
int query(int L, int R, int l, int r, int rt) {
    if (L <= l && r <= R) return tree[rt];
    // dbg(l, r, rt);
    int m = (l + r) >> 1, ans = 0; pushdown(rt, m - l + 1, r - m);
    if (L <= m) ans += query(L, R, lson);
    if (R > m) ans += query(L, R, rson);
    return ans;
}

ll pre[maxn][2];

int main(){
    scanf("%d%d%d", &n, &q, &x);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    build(1, n, 1);
    int m1 = 0, m2 = 0;
    for (int i = 1; i <= n; i++) {
        if (a[i] <= x) m1++, pre[m1][0] = pre[m1 - 1][0] + a[i];
        else m2++, pre[m2][1] = pre[m2 - 1][1] + a[i];
    }
    int op, l, r;
    while (q--) {
        scanf("%d%d%d", &op, &l, &r);
        if (op == 1) {
            int p1 = 0, p2 = query(1, r, 1, n, 1);
            if (l > 1) p1 = query(1, l - 1, 1, n, 1);
            ll ans = pre[p2][1] - pre[p1][1] + pre[r - p2][0] - pre[l - 1 - p1][0];
            printf("%lld\n", ans);
        }
        if (op == 2) {
            int c = query(l, r, 1, n, 1);
            if (l <= r - c) update(l, r - c, 0, 1, n, 1);
            if (r - c + 1 <= r) update(r - c + 1, r, 1, 1, n, 1);
            // dbg(c, query(l, r, 1, n, 1));
        }
        if (op == 3) {
            int c = query(l, r, 1, n, 1);
            if (l <= l + c - 1) update(l, l + c - 1, 1, 1, n, 1);
            if (l + c <= r) update(l + c, r, 0, 1, n, 1);
            // dbg(c, query(l, r, 1, n, 1));
        }
    }
    return 0;
}
```


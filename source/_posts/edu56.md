---
mathjax: true
title: Educational Round 56 题解
tags:
  - Codeforces
  - Solution
  - Data Structure
  - SegTree
  - Graph
categories:
  - Codeforces
  - Educational Round
date: 2019-03-02 20:56:13
---

# A Dice Rolling

# B Letters Rearranging

# C Mishka and the Last Exam

有一个长度为偶数的单调不减序列 $a$，给定了一个长度为其一半的序列 $b$，满足

$$
b_i=a_i+a_{n-i+1}
$$

要求恢复出序列 $a$。

贪心，左边的数必须尽量小。

# D Beautiful Graph

给一个图上每个点染上 $1,2,3$ 三种颜色，要求相邻顶点的颜色和为奇数，求方案数。

每个点分奇偶两种状态，一个联通块取定一个点后，块内奇偶性确定，遍历两遍即可。

注意遍历时的判环和判是否走过某个点写法。

遍历图都不会写了 T^T。

# E Intersection of Permutations

给定两个序列 $a,b$，有两种操作。

交换序列 $b$ 的两个数，询问序列 $b$ 的 $[l2,r2]$ 区间内有多少个数在序列 $a$ 的 $[l1,r1]$ 区间内。

在序列 $b$ 上每个前缀维护主席树，再带上修改操作，树状数组套主席树即可。

cdq 分治不会写 T^T。

# G Multidimensional Queries	

维护一个序列，每个位置是一个 $k$ 维向量（$1\le k \le 5$）。

有两种操作，单点覆盖和询问区间两点间最大的曼哈顿距离。

建 $32$ 棵线段树即可。

<!--more-->

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

int x;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        cin >> x;
        cout << x / 2 << endl;
    }
    return 0;
}
```

## B

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

string s;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        cin >> s;
        int flag = 1;
        for (int i = 1; i < s.length(); i++) if (s[i] != s[0]) {
            flag = 0; break;
        }
        if (flag) puts("-1");
        else {
            sort(s.begin(), s.end());
            cout << s << endl;
        }
    }
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#include <assert.h>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 200000 + 5;

int n;
ll a[maxn], b[maxn];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n / 2; i++) scanf("%I64d", b + i);
    a[1] = 0; a[n] = b[1];
    for (int i = 2; i <= n / 2; i++) {
        if (b[i] - a[i - 1] <= a[n - i + 2]) {
            a[i] = a[i - 1]; a[n - i + 1] = b[i] - a[i - 1];
        } else {
            a[n - i + 1] = a[n - i + 2];
            a[i] = b[i] - a[n - i + 2];
        }
        assert(a[i] >= a[i - 1] && a[n - i + 2] >= a[n - i + 1]);
    }
    for (int i = 1; i <= n; i++) printf("%I64d ", a[i]);
    return 0;
}
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
#ifdef XLor
  #define dbg(args...) do {cout << #args << " -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 300000 + 5;

vector<int> edge[maxn];
int n, m, id, vis[maxn], tag[maxn];

ll dfs(int u, int tp) {
    vis[u] = id;
    int nx = (tp == 1 ? 2 : 1); ll r = nx;
    for (int& v: edge[u]) {
        if (tag[v]) {
            if (tag[v] == tp) return 0;
            continue;
        }
        if (vis[v] == id) continue;
        // dbg(u, v);
        tag[v] = nx;
        r = r * dfs(v, nx) % mod;
        tag[v] = 0;
    }
    // dbg(u, r);
    return r;
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        for (int i = 1; i <= n; i++) edge[i].clear(), vis[i] = 0;
        while (m--) {
            int u, v; scanf("%d%d", &u, &v);
            edge[u].push_back(v); edge[v].push_back(u);
        }
        ll ans = 1;
        for (int i = 1; i <= n; i++) if (!vis[i]) {
            // ans = ans * (dfs(i, 1) + dfs(i, 2)) % mod;
            ll tot = 0;
            id = tag[i] = 1; tot += dfs(i, 1); 
            // dbg(tot);
            // dbg('?');
            id = tag[i] = 2; tot += dfs(i, 2);
            tag[i] = 0;
            // dbg(tot);
            ans = ans * tot % mod;
        }
        printf("%I64d\n", ans);
    }
    return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define lson l, m, ls[rt]
#define rson m + 1, r, rs[rt]
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 200000 + 5;

vector<int> nodes; int nid;
int tot, root[maxn], ls[maxn * 150], rs[maxn * 150], cnt[maxn * 150];
int node() {
    if (nodes.empty()) return ++nid;
    int r = nodes.back(); nodes.pop_back();
    return r;
}
void del(int& x) {
    ls[x] = rs[x] = cnt[x] = 0;
    nodes.push_back(x); x = 0;
}

int n, q, a[maxn], b[maxn], pos[maxn];

int l1, r1, l2, r2;
void update(int i, int x, int l, int r, int& rt) {
    if (!rt) rt = node(); cnt[rt] += x;
    if (l == r) {
        if (!cnt[rt]) del(rt), rt = 0;
        return ;
    }
    int m = (l + r) >> 1;
    if (i <= m) update(i, x, lson);
    else update(i, x, rson);
    if (!cnt[rt]) del(rt), rt = 0;
}
int query(int L, int R, int l, int r, int rt) {
    if (!rt) return 0;
    if (L <= l && r <= R) return cnt[rt];
    int m = (l + r) >> 1, ans = 0;
    if (L <= m) ans += query(L, R, lson);
    if (R > m) ans += query(L, R, rson);
    return ans;
}
inline int lowbit(int x) { return x & -x; }
inline void update(int i, int x) {
    int p = pos[b[i]];
    for (; i <= n; i += lowbit(i)) update(p, x, 1, n, root[i]);
}
inline int query() {
    int ans = 0;
    for (int i = l2 - 1; i > 0; i -= lowbit(i)) ans -= query(l1, r1, 1, n, root[i]);
    for (int i = r2; i > 0; i -= lowbit(i)) ans += query(l1, r1, 1, n, root[i]);
    return ans;
}

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= n; i++) scanf("%d", a + i), pos[a[i]] = i;
    for (int i = 1; i <= n; i++) scanf("%d", b + i);
    for (int i = 1; i <= n; i++) update(i, 1);
    int op, x, y;
    while (q--) {
        scanf("%d", &op);
        if (op == 1) {
            scanf("%d%d%d%d", &l1, &r1, &l2, &r2);
            printf("%d\n", query());
        }
        if (op == 2) {
            scanf("%d%d", &x, &y);
            update(x, -1); update(y, -1);
            swap(b[x], b[y]);
            update(x, 1); update(y, 1);
        }
    }
    return 0;
}
```

## G

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 200000 + 5;

int n, k, q, a[maxn][32], tmp[5];

struct SegTree {
    int mx[maxn * 4], mn[maxn * 4];
    void pushup(int rt) { 
        mx[rt] = max(mx[rt << 1], mx[rt << 1 | 1]);
        mn[rt] = min(mn[rt << 1], mn[rt << 1 | 1]);
    }
    void build(int k, int l, int r, int rt) {
        if (l == r) {
            mx[rt] = mn[rt] = a[l][k]; return ;
        }
        int m = (l + r) >> 1;
        build(k, lson); build(k, rson);
        pushup(rt);
    }
    void update(int i, int x, int l, int r, int rt) {
        if (l == r) {
            mx[rt] = mn[rt] = x; return ;
        }
        int m = (l + r) >> 1;
        if (i <= m) update(i, x, lson);
        else update(i, x, rson);
        pushup(rt);
    }
    int qmin(int L, int R, int l, int r, int rt) {
        if (L <= l && r <= R) return mn[rt];
        int m = (l + r) >> 1, ans = 1e9;
        if (L <= m) ans = min(ans, qmin(L, R, lson));
        if (R > m) ans = min(ans, qmin(L, R, rson));
        return ans;
    }
    int qmax(int L, int R, int l, int r, int rt) {
        if (L <= l && r <= R) return mx[rt];
        int m = (l + r) >> 1, ans = -1e9;
        if (L <= m) ans = max(ans, qmax(L, R, lson));
        if (R > m) ans = max(ans, qmax(L, R, rson));
        return ans;
    }
} tr[32];


int main() {
    scanf("%d%d", &n, &k);
    int ss = 1 << k;
    for (int i = 1; i <= n; i++) {
        for (int j = 0; j < k; j++) scanf("%d", &tmp[j]);
        for (int s = 0; s < ss; s++) {
            int c = 0;
            for (int j = 0; j < k; j++) if (s & (1 << j)) c += tmp[j]; else c -= tmp[j];
            a[i][s] = c;
        }
    }
    for (int s = 0; s < ss; s++) tr[s].build(s, 1, n, 1);
    scanf("%d", &q);
    int op, l, r;
    while (q--) {
        scanf("%d%d", &op, &l);
        if (op == 1) {
            for (int i = 0; i < k; i++) scanf("%d", &tmp[i]);
            for (int s = 0; s < ss; s++) {
                int c = 0;
                for (int j = 0; j < k; j++) if (s & (1 << j)) c += tmp[j]; else c -= tmp[j];
                tr[s].update(l, c, 1, n, 1);
            }
        }
        if (op == 2) {
            scanf("%d", &r);
            int ans = 0;
            for (int s = 0; s < ss; s++) {
                ans = max(ans, tr[s].qmax(l, r, 1, n, 1) - tr[s].qmin(l, r, 1, n, 1));
            }
            printf("%d\n", ans);
        }
    }
    return 0;
}
```
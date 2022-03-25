---
mathjax: true
title: Educational Round 61 题解
tags:
  - Codeforces
  - Solution
  - Binary Search
  - DP
  - Tree
  - Data Structure
categories:
  - Codeforces
  - Educational Round
date: 2019-03-06 16:46:08
---

# A Regular Bracket Sequence

# B Discounts

# C Painting the Fence

给定长度 $n$ 的序列上的 $q$ 条线段，从中删除两端使得剩余被覆盖位置数量最大。

枚举两个线段，要求计算这两段上有多少个位置只被覆盖一次，线段重合部分上有多少个位置只被覆盖两次。

# D Stressful Training 

给定 $n$ 台电脑，每台电脑初始有电量 $a_i$，每小时消耗电量 $b_i$，有一个充电器每小时可为一台电脑充电，求使得所有电脑工作 $k$ 天所需要充电器的最小每小时充电量。

考虑二分，贪心构造。每天将没电时刻最早的那台电脑拿出来充电，并且如果某天有多台电脑在此刻没电，那么构造失败。

注意：二分边界，使用数组和指针来维护，而不是上数据结构！

二分不出来的我 T^T。
 
# F Clear the String

给定一个字符串 $s$，每次可以删除一个相同的连续段，求最小删除次数。

区间 DP 裸题。

考虑 $f[l][r]$ 表示删除区间 $[l,r]$ 的花费。

转移时，若 $s[l]=s[r]$

$$
f[l][r]=min(f[l][r-1],f[l+1][r],f[l+1][r-1]+1)
$$

否则

$$
f[l][r]=min(f[l][j],f[j+1][r])
$$

区间 DP 不出来的我 T^T。

# G Greedy Subsequences

定义贪心子序列 $a_{p_1},a_{p_2},a_{p_3},\dots$ ，满足，$p_{i+1}$ 是大于 $p_i$ 的第一个满足 $a_{p_{i+1}} > a_{p_i}$ 的下标。

求序列 $a$ 的每一个长度为 $k$ 连续子区间的最长贪心子序列长度。

可以发现，对于整个区间而言，贪心子序列实际上构成的一个树的结构，每个点都会连向唯一一个点，且可能存在前面的若干点连到这个点上。

考虑维护一个单调递减的单调栈，来建出这棵树（森林）。

对于每次询问实际上是在顶点编号在 $[i,i+k-1]$ 范围内的对应虚树上，询问树（森林）的最大高度。

使用线段树维护每个点的高度，区间左指针移动表示虚树上删除了一个点，右指针移动表示虚树扩大，需要给对应子树区间内所有点高度 $+1$。

妙啊！

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

int c1, c2, c3, c4;

int main() {
    cin >> c1 >> c2 >> c3 >> c4;
    if (c1 == c4) {
        if (c1 == 0) {
            if (c3) puts("0");
            else puts("1");
        } else puts("1");
    } else puts("0");
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
const int maxn = 300000 + 5;

int n, m, q, a[maxn];

int main() {
    scanf("%d", &n);
    ll sum = 0;
    for (int i = 1; i <= n; i++) scanf("%d", a + i), sum += a[i];
    sort(a + 1, a + n + 1); reverse(a + 1, a + n + 1);
    scanf("%d", &m);
    for (int i = 1; i <= m; i++) {
        scanf("%d", &q);
        printf("%I64d\n", sum - a[q]);
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
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

int n, q, l[maxn], r[maxn], vis[maxn], cnt[maxn][3];

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= q; i++) {
        scanf("%d%d", l + i, r + i);
        for (int j = l[i]; j <= r[i]; j++) vis[j]++;
    }
    int ans = 0, res = 0;
    for (int i = 1; i <= n; i++) {
        if (vis[i]) ans++;
        if (vis[i] == 1) cnt[i][0]++;
        if (vis[i] == 2) cnt[i][1]++;
        cnt[i][0] += cnt[i - 1][0];
        cnt[i][1] += cnt[i - 1][1];
    }
    for (int i = 1; i <= q; i++) for (int j = i + 1; j <= q; j++) {
        if (r[i] < l[j] || r[j] < l[i]) {
            int tot = ans;
            tot -= cnt[r[i]][0] - cnt[l[i] - 1][0];
            tot -= cnt[r[j]][0] - cnt[l[j] - 1][0];
            res = max(res, tot);
        } else {
            int l1 = min(l[i], l[j]), l2 = max(l[i], l[j]);
            int r1 = min(r[i], r[j]), r2 = max(r[i], r[j]);
            int tot = ans;
            tot -= cnt[l2 - 1][0] - cnt[l1 - 1][0];
            tot -= cnt[r2][0] - cnt[r1][0];
            tot -= cnt[r1][0] - cnt[l2 - 1][0];
            tot -= cnt[r1][1] - cnt[l2 - 1][1];
            res = max(res, tot);
        }
    }
    cout << res << endl;
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
#include <queue>
#include <functional>
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
typedef pair<ll,int> PII;
const int mod = 998244353;
const int maxn = 200000 + 5;

int n, k;
ll a[maxn], b[maxn], c[maxn];

vector<int> bag[maxn]; int tot = 1;
void insert(ll i, int id) {
    if (i > k) return ;
    bag[i].push_back(id);
}
int pop() {
    while (tot <= k && bag[tot].empty()) tot++;
    if (tot > k) return -1;
    int id = bag[tot].back(); bag[tot].pop_back();
    return id;
}
int top() {
    while (tot <= k && bag[tot].empty()) tot++;
    if (tot > k) return -1;
    return bag[tot].back();
}

int check(ll x) {
    for (int i = 1; i <= k; i++) bag[i].clear(); tot = 1;
    for (int i = 1; i <= n; i++) {
        c[i] = a[i]; 
        insert(a[i] / b[i] + 1, i);
    }
    for (int i = 1; i <= k; i++) {
        int id = pop();
        if (id == -1) return 1;
        c[id] += x; 
        insert(c[id] / b[id] + 1, id);
        if (!bag[i].empty()) return 0;
    }
    int id = pop(); if (id == -1) return 1;
    if (c[id] / b[id] + 1 <= k) return 0;
    return 1;
}

int main() {
    scanf("%d%d", &n, &k); k--;
    for (int i = 1; i <= n; i++) scanf("%I64d", a + i);
    for (int i = 1; i <= n; i++) scanf("%I64d", b + i);
    ll l = 0, r = 5e12, ans = -1;
    while (l <= r) {
        ll m = (l + r) >> 1;
        if (check(m)) ans = m, r = m - 1;
        else l = m + 1;
    }
    cout << ans << endl;
    return 0;
}
```

## F

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
const int maxn = 1000 + 5;

int n, f[maxn][maxn]; char s[maxn];

int main() {
    scanf("%d%s", &n, s + 1);
    for (int i = 1; i <= n; i++) for (int j = 1; j <= n; j++) f[i][j] = 1e9;
    for (int i = 1; i <= n; i++) f[i][i] = 1;
    for (int len = 2; len <= n; len++) {
        for (int l = 1; l + len - 1 <= n; l++) {
            int r = l + len - 1;
            if (s[l] == s[r]) {
                f[l][r] = min(f[l + 1][r - 1] + 1, min(f[l + 1][r], f[l][r - 1]));
            } else {
                for (int i = l; i < r; i++) f[l][r] = min(f[l][r], f[l][i] + f[i + 1][r]);
            }
        }
    }
    cout << f[1][n] << endl;
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
#include <set>
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
#ifdef XLor
  #define dbg(args...) do {cout << #args << " -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1e8 + 7;
const int maxn = 1000000 + 5;

vector<int> edge[maxn];
int n, k, a[maxn], fa[maxn];

int tin[maxn], tout[maxn], tot;
void dfs(int u, int f) {
    tin[++tot] = u;
    for (int& v: edge[u]) dfs(v, u);
    tout[u] = tot;
}

ll tr[maxn << 2], tag[maxn << 2];
void pushdown(int rt) {
    ll x = tag[rt];
    tr[rt << 1] += x; tr[rt << 1 | 1] += x;
    tag[rt << 1] += x; tag[rt << 1 | 1] += x;
    tag[rt] = 0;
}
void update(int L, int R, int x, int l, int r, int rt) {
    if (L > r || R < l) return ;
    if (L <= l && r <= R) {
        tr[rt] += x; tag[rt] += x; return ;
    }
    // dbg(l, r, rt);
    int m = (l + r) >> 1; pushdown(rt);
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    tr[rt] = max(tr[rt << 1], tr[rt << 1 | 1]);
}

int main() {
    scanf("%d%d", &n, &k);
    vector<int> st;
    for (int i = 1; i <= n; i++) {
        scanf("%d", a + i);
        while (!st.empty() && a[i] > a[st.back()]) {
            fa[st.back()] = i; 
            edge[i].push_back(st.back());
            st.pop_back();
        }
        st.push_back(i);
    }
    for (int i = 1; i <= n; i++) if (!fa[i]) dfs(i, 0);
    for (int i = 1; i <= k; i++) update(tin[i], tout[i], 1, 1, n, 1);
    printf("%I64d ", tr[1]);
    for (int i = k + 1; i <= n; i++) {
        update(tin[i], tout[i], 1, 1, n, 1);
        update(tin[i - k], tin[i - k], -inf, 1, n, 1);
        printf("%I64d ", tr[1]);
    }
    return 0;
}
```
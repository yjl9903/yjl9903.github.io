---
mathjax: true
title: Codeforces Round 536 题解
tags:
  - Codeforces
  - Solution
  - DP
categories:
  - Codeforces
date: 2019-02-01 13:31:40
---

# A Lunar New Year and Cross Counting

模拟一下（第一发写错了符号还能过样例是什么鬼？

# B Lunar New Year and Food Ordering

# C Lunar New Year and Number Division

把 $n$ 个划分成好多个集合（ $n$ 为偶数），记 $S_j$ 表示第 $j$ 个集合内数的和的平方，要求最小化 $\sum S_j$。

显然，排个序，最大的和最小的组成一组即可（容易证明）。

# D Lunar New Year and a Wander

一个人在图上走，每次走到一个未经过的点，将其记录下来，求走遍这个图记录下的字典序最小的序列。

显然，直接贪心地 $dfs$ 是不行的。我们可以用优先队列做一个类似 $Prim$ 的过程，维护一下当前可以走到哪些没有走过的点。

# E Lunar New Year and Red Envelopes

给一个长度为 $n$ 的时间轴，有 $k$ 个红包，每个红包可以在 $[s,t]$ 的时间内被领取，领取后获得价值 $w$，领取后直到 $d+1$ 时间才能继续领红包。

一个人贪心的领取红包，能领红包的话，就领价值最大且 $d$ 最大的那个。

你现在有 $m$ 次机会阻止他在某一个时间拿红包，你非常聪明，求这个人能获得的最小价值。

首先，线段树（ $set$ + 扫描线）预处理每个位置拿的红包 $a_i$。

考虑 $dp[i][j]$ 表示在前 $i$ 个时间内，被阻止了 $j$ 次获得的最小价值，转移方程

$$
dp[1][0]=0 \\
dp[i+1][j+1]=min(dp[i+1][j+1],dp[i][j]) \\
dp[a[i].d + 1][j]=min(dp[a[i].d+1][j], dp[i][j]+a[i].w)
$$

<!--more-->

# 代码

## A

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
const int maxn = 500 + 5;

char map[maxn][maxn];
int n;

int main(){
    scanf("%d", &n);
    for (int i = 0; i < n; i++) scanf("%s", map[i]);
    int ans = 0;
    for (int i = 1; i < n - 1; i++) {
        for (int j = 1; j < n - 1; j++) {
            if (map[i][j] == 'X' && map[i - 1][j - 1] == 'X' && map[i + 1][j - 1] == 'X' && 
                map[i - 1][j + 1] == 'X' && map[i + 1][j + 1] == 'X') ans++;
        }
    }
    cout << ans << endl;
    return 0;
}
```

## B

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <set>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

int n, m, a[maxn], c[maxn], vis[maxn];
set<PII> st;

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = 1; i <= n; i++) scanf("%d", c + i);
    for (int i = 1; i <= n; i++) {
        st.insert({c[i], i});
    }
    int flag = 0;
    for (int i = 1, t, d; i <= m; i++) {
        scanf("%d%d", &t, &d);
        if (flag) {
            puts("0"); continue;
        }
        if (a[t] - d >= 0) {
            printf("%I64d\n", 1ll * c[t] * d);
            a[t] -= d;
            if (a[t] == 0) {
                st.erase({c[t], t});
            }
        } else {
            ll sum = 1ll * c[t] * a[t];
            d -= a[t]; a[t] = 0;
            if (st.count({c[t], t})) st.erase({c[t], t});
            while (d > 0) {
                if (st.empty()) {
                    flag = 1; break;
                }
                PII t = *st.begin();
                int id = t.second;
                if (a[id] - d >= 0) {
                    sum += 1ll * c[id] * d;
                    a[id] -= d; d = 0;
                    if (a[id] == 0) {
                        st.erase({c[id], id});
                    }
                } else {
                    sum += 1ll * c[id] * a[id];
                    d -= a[id]; a[id] = 0;
                    if (st.count({c[id], id})) st.erase({c[id], id});
                }
            }
            if (flag) puts("0");
            else printf("%I64d\n", sum);
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
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 300000 + 5;

int n, a[maxn];

int main(){
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    sort(a + 1, a + 1 + n);
    ll sum = 0;
    for (int i = 1; i <= n / 2; i++) {
        ll t = a[i] + a[n - i + 1];
        sum += t * t;
    }
    cout << sum << endl;
    return 0;
}
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <queue>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

vector<int> edge[maxn];
vector<int> ans;
int n, m, vis[maxn];

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 1, u, v; i <= m; i++) {
        scanf("%d%d", &u, &v); 
        edge[u].push_back(v); edge[v].push_back(u);
    }
    priority_queue<int> pq; pq.push(-1); vis[1] = 1;
    while (!pq.empty()) {
        int t = -pq.top(); pq.pop();
        ans.push_back(t);
        for (int& v: edge[t]) {
            if (vis[v]) continue;
            pq.push(-v); vis[v] = 1;
        }
    }
    for (int& x: ans) printf("%d ", x);
    return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <utility>
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
typedef pair<int, int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;
const ll inf = 1e18;

PII tree[maxn << 2], tag[maxn << 2];
void pushup(int rt) {
    tree[rt] = max(tree[rt << 1], tree[rt << 1 | 1]);
}
void pushdown(int rt) {
    if (tag[rt].first == 0 && tag[rt].second == 0) return;
    tree[rt << 1] = max(tree[rt << 1], tag[rt]);
    tree[rt << 1 | 1] = max(tree[rt << 1 | 1], tag[rt]);
    tag[rt << 1] = max(tag[rt << 1], tag[rt]);
    tag[rt << 1 | 1] = max(tag[rt << 1 | 1], tag[rt]);
    tag[rt] = make_pair(0, 0);
}
void build(int l, int r, int rt) {
    tag[rt] = tree[rt] = make_pair(0, 0);
    if (l == r) return;
    int m = (l + r) >> 1;
    build(lson); build(rson);
    pushup(rt);
}
void update(int L, int R, PII x, int l, int r, int rt) {
    if (L <= l && r <= R) {
        tree[rt] = max(tree[rt], x);
        tag[rt] = max(tag[rt], x);
        return;
    }
    int m = (l + r) >> 1;
    pushdown(rt);
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    pushup(rt);
}
PII query(int p, int l, int r, int rt) {
    if (l == r) return tree[rt];
    int m = (l + r) >> 1;
    pushdown(rt);
    if (p <= m) return query(p, lson);
    else return query(p, rson);
}

int n, m, k, s[maxn], t[maxn], d[maxn], w[maxn];
ll f[maxn][300];
PII a[maxn];

int main(){
    scanf("%d%d%d", &n, &m, &k);
    build(1, n, 1);
    for (int i = 1; i <= k; i++) {
        scanf("%d%d%d%d", s + i, t + i, d + i, w + i);
        update(s[i], t[i], {w[i], d[i]}, 1, n, 1);
    }
    for (int i = 1; i <= n; i++) {
        a[i] = query(i, 1, n, 1);
        if (a[i].second == 0) {
            a[i].second = i;
        }
    }
    for (int i = 0; i <= n + 1; i++) for (int j = 0; j <= m + 1; j++) f[i][j] = inf;
    ll ans = inf;
    f[1][0] = 0;
    for (int j = 0; j <= m; j++) {
        for (int i = 1; i <= n; i++) {
            f[a[i].second + 1][j] = min(f[a[i].second + 1][j], f[i][j] + a[i].first);
            f[i + 1][j + 1] = min(f[i + 1][j + 1], f[i][j]);
        }
        ans = min(ans, f[n + 1][j]);
        for (int i = 1; i <= n + 1; i++) dbg(f[i][j]);
    }
    cout << ans << endl;
    return 0;
}
```
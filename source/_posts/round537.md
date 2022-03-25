---
mathjax: true
title: Codeforces Round 537 题解
tags:
  - Codeforces
  - Solution
  - Tree
categories:
  - Codeforces
date: 2019-02-06 00:16:31
---

# A Superhero Transformation

# B Average Superhero Gang Power

给一个序列，你有 $m$ 次操作，每次操作可以选择删除一个数或者给某一个数 $+1$，但是一个数最多被加 $k$ 次，要求序列平均值的最大值。

显然，排个序，枚举删除了多少个，剩下的全部都是加到后面即可。

注意，加到哪个数并不重要，总量不超过上限即可。

Hackforces，插了 $5$ 个，最后又 FST 了上千个2333。

# C Creative Snap

一排长度为 $2^n$ 的位置，某些位置上有人占领（一个位置可以被多人占领），现在你要毁灭整个区间，你有两种毁灭方式：

1. 将当前要毁灭的区间分成左右两部分，分别毁灭；
2. 毁灭当前区间，如果区间内没人占领，费用为 $A$，如果区间内有人占领，费用为 $B \times m \times l$，其中 $A,B$ 为常数，$m$ 为当前区间内的人数，$l$ 为区间长度。

求最小花费。

直接根据题意模拟分治过程即可。

关键是要询问一个区间内的人数，对所有人的位置排序，询问时做两次二分即可。

分治时，注意两个细节：

1. 区间内没人时，直接返回，无需分治；
2. 区间长度为 $1$ 时，注意这个位置可能有很多人。

第二点 $wa$ 了一年 T^T。

# E Tree

给一棵无根树，现在有 $q$ 次询问，每次询问无根树以 $r$ 为根时，有 $k$ 个关键点，将关键点分为最多 $m$ 块，要求满足每块内没有点对互为这棵有根树上的子孙，求这样的分块方案。

其中 $n, q,\sum k \le 1e5,m\le300$。

首先，考虑根固定为 $1$ 的做法，将关键点按照 $dfs$ 序排序，设 $h[i]$ 表示关键内是 $i$ 的祖先节点的个数，$dp[i][j]$ 表示前 $i$ 个点分成 $j$ 块的方案数，有转移方程

$$
dp[i][j]=dp[i-1][j-1]+(j-h[i])dp[i-1][j](j>h[i])
$$

因此，对于每个关键点，我们只需要知道有多少关键点是它的祖先即可，这部分可以搞出 $dfs$ 序，在树状数组上差分即可。

之后，我们考虑这个 $dp$ 的正确性条件，必须满足计算 $i$ 的 $dp$ 状态时，他的祖先必须全部都计算过了。然后，这个条件实际上不需要用 $dfs$ 序来排序，可以考虑用深度或者说祖先的个数来排序，按照这个顺序 $dp$ 即可。

最后，我们考虑换根操作，换根后我们实质上是询问了，某个关键点到根的路径上出现了多少关键点，这个部分用 $LCA​$ 差分即可。

好！

<!--more-->

PS：树剖第二遍 $dfs$ 写挂了两天，竟然一直没发现还行。

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
const int maxn = 100000 + 5;

char s[maxn], t[maxn];

int main(){
    scanf("%s%s", s, t);
    int n = strlen(s), m = strlen(t);
    if (n != m) return puts("No"), 0;
    for (int i = 0; i < n; i++) {
        if (s[i] == t[i]) continue;
        int cnt = 0;
        if (s[i] == 'a' || s[i] == 'e' || s[i] == 'i' || s[i] == 'o' || s[i] == 'u') cnt++;
        if (t[i] == 'a' || t[i] == 'e' || t[i] == 'i' || t[i] == 'o' || t[i] == 'u') cnt++;
        if (cnt == 1) return puts("No"), 0;
    }
    puts("Yes");
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 100000 + 5;

int n, k, m, a[maxn];

int main(){
    scanf("%d%d%d", &n, &k, &m);
    ll sum = 0;
    for (int i = 0; i < n; i++) scanf("%d", a + i), sum += a[i];
    sort(a, a + n);
    ll pre = 0; double ans = 0;
    for (int i = 0; i < n && i <= m; i++) {
        ll t = sum - pre;
        // cout << t << ' ';
        if (m - i <= 1ll * k * (n - i)) t += m - i;
        else t += 1ll * k * (n - i);
        // cout << t << endl;
        ans = max(ans, (double)t / (double)(n - i));
        pre += a[i];
    }
    printf("%.10lf\n", ans);
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
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 100000 + 5;

int n, k, A, B, pos[maxn];

int cal(ll l, ll r) {
    int k1 = lower_bound(pos, pos + k, l) - pos;
    int k2 = upper_bound(pos, pos + k, r) - pos;
    return k2 - k1;
}

ll solve(ll l, ll r) {
    // cout << l << ' ' << r <<endl;
    if (l >= r) {
        int cnt = cal(l, r);
        if (cnt) return 1ll * B * cnt;
        else return A;
    }
    int cnt = cal(l, r);
    ll tot;
    if (cnt) tot = 1ll * B * cnt * (r - l + 1);
    else return A;
    ll m = (l + r) / 2;
    // cout << l << ' ' << r << endl;
    return min(tot, solve(l, m) + solve(m + 1, r));
}

int main() {
    scanf("%d%d%d%d", &n, &k, &A, &B);
    for (int i = 0; i < k; i++) scanf("%d", pos + i);
    sort(pos, pos + k);
    cout << solve(1, 1 << n) << endl;
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

vector<int> edge[maxn];
int n, q, in[maxn], out[maxn];

namespace hld {
    int dep[maxn], fa[maxn], siz[maxn], son[maxn], top[maxn], tot;
    void dfs(int u, int f) {
        dep[u] = dep[f] + 1; fa[u] = f; siz[u] = 1;
        in[u] = ++tot; int m = -1;
        for (int& v: edge[u]) {
            if (v == f) continue;
            dfs(v, u);
            siz[u] += siz[v];
            if (siz[v] > m) son[u] = v, m = siz[v];
        }
        out[u] = tot;
    }
    void dfs(int u, int f, int tp) {
        top[u] = tp;
        if (!son[u]) return;
        dfs(son[u], u, tp);
        for (int& v: edge[u]) {
            if (v == f || v == son[u]) continue;
            dfs(v, u, v);
        }
    }
    void init() {
        dfs(1, 0); dfs(1, 0, 1);
    }
    int qlca(int u, int v) {
        while (top[u] != top[v]) {
            if (dep[top[u]] < dep[top[v]]) swap(u, v);
            u = fa[top[u]];
        }
        return dep[u] < dep[v] ? u : v;
    }
};

int tree[maxn];
inline int lowbit(int x) { return x & -x; }
inline void update(int i, int x) {
    while (i < maxn) {
        tree[i] += x; i += lowbit(i);
    }
}
inline void update(int L, int R, int x) {
    update(L, x); update(R + 1, -x);
}
inline int query(int i) {
    int r = 0;
    while (i > 0) { r += tree[i]; i -= lowbit(i); }
    return r;
}

ll dp[maxn];
int a[maxn], f[maxn], mark[maxn];

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    } hld::init();
    int k, m, r;
    while (q--) {
        scanf("%d%d%d", &k, &m, &r);
        for (int i = 1; i <= k; i++) {
            scanf("%d", a + i);
            update(in[a[i]], out[a[i]], 1);
            mark[a[i]] = 1;
        }
        for (int i = 1; i <= k; i++) {
            int lca = hld::qlca(a[i], r);
            f[i] = query(in[a[i]]) + query(in[r]) - 2 * query(in[lca]) + mark[lca] - 1;
        } 
        sort(f + 1, f + k + 1);
        dp[0] = 1;
        for (int i = 1; i <= k; i++) {
            for (int j = m; j >= 0; j--) {
                if (j <= f[i]) dp[j] = 0;
                else dp[j] = (dp[j - 1] + 1ll * (j - f[i]) * dp[j] % mod) % mod;
            }
        }
        ll ans = 0;
        for (int i = 1; i <= m; i++) ans = (ans + dp[i]) % mod, dp[i] = 0; 
        printf("%I64d\n", ans);
        for (int i = 1; i <= k; i++) {
            update(in[a[i]], out[a[i]], -1);
            mark[a[i]] = 0;
        }
    }
    return 0;
}
```
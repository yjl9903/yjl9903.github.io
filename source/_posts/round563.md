---
mathjax: true
title: Codeforces Round 563 题解
tags:
  - Codeforces
  - Solution
  - Tree
  - Math
  - Number Theory
  - Bitmasks
  - Interaction
categories:
  - Codeforces
date: 2019-06-04 02:02:39
---

F 的做法实在是太优美了www。

# A Ehab Fails to Be Thanos

# B Ehab Is an Odd Person

给定一个序列，你仅可以交换奇偶不同的两个位置，求字典序最小的序列。

若所有数奇偶性相同，显然无法操作。否则，可以两两任意操作，排序即可。

# C Ehab and a Special Coloring Problem

给 $2$ 到 $n$ 所有数字染色，要求满足互质的两个数颜色不同。

考虑类似于调和级数那样子染色即可。

# D Ehab and the Expected XOR Problem

在 $1$ 到 $2^n$ 内选一些数字，满足任意子串异或和不为 $0$ 或 $x$，求长度最长的序列。

转化为构造前缀和，显然前缀和两两不同且没有两个异或起来为 $x$，显然选了一个就不能选另外一个异或起来为 $x$，容易构造。

# F Ehab and the Big Finale

给定一棵树，猜一个隐藏的点 $x$。

有 $36$ 次询问，询问一个点到 $x$ 的距离，询问 $x$ 的一个祖先到 $x$ 的路径上下一个点。

最开始时，询问到根的距离，这样就有很多备选解，考虑将这个解空间缩小。

再考虑给定的询问方式，我们于是需要加速一个点往下跳转的过程，并且保证正确性，而不是一步一步走。

考虑轻重链剖分，当前考虑的根为 $u$，重链的底端为 $v$，考虑 $v$ 和 $x$ 的最近公共祖先，联想树上差分距离这个过程的逆，可以得到这个 LCA。

如果 LCA 不是答案，那么将 LCA 往下移一步递归地解决即可。

显然，轻链的个数是 $O(\log n)$ 的，每次最多只能询问两次。

注意：有一个询问可以在缩小问题规模时维护出来，无需单独询问。

类似于轻重链剖分的做法，也可以用重心分解来加速跳转。

讨论一下 $x$ 是不是在当前的重心内，在重心内就往下走一步，否则取当前的子问题为重心的父亲，并标记删除。

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
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, a[maxn];

int main() {
    cin >> n;
    for (int i = 1; i <= 2 * n; i++) cin >> a[i];
    sort(a + 1, a + 1 + 2 * n);
    ll sum = 0, sum2 = 0;
    for (int i = 1; i <= n; i++) sum += a[i];
    for (int i = n + 1; i <=2 * n; i++) sum2 += a[i];
    if (sum == sum2) puts("-1");
    else {
        for (int i = 1; i <= 2 * n; i++) cout << a[i] << ' ';
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
#include <set>
#include <map>
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
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, a[maxn];
set<PII> s[2];

int main() {
    scanf("%d", &n);
    int x = 0, f = 0;
    for (int i = 1; i <= n; i++) {
        scanf("%d", a + i);
        if (i == 1) x = a[i] % 2;
        else {
            if (a[i] % 2 != x) f = 1;
        }
        // s[a[i] % 2].insert({a[i], i});
    }
    if (f) sort(a + 1, a + 1 + n);
    // for (int i = 1; i <= n; i++) {
    //     dbg(s[0].size(), s[1].size());
    //     int x = a[i] % 2; x ^= 1;
    //     if (s[x ^ 1].count({a[i], i})) s[x ^ 1].erase({a[i], i});
    //     if (s[x].empty()) continue;
    //     auto it = s[x].begin();
    //     if (a[i] < it->first) continue;
    //     a[it->second] = a[i];
    //     s[x ^ 1].insert({a[it->second], it->second});
    //     a[i] = it->first;
    //     s[x].erase(it);
    // }
    for (int i = 1; i <= n; i++) printf("%d ", a[i]);
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
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, a[maxn];

int main() {
    cin >> n;
    set<int> s;
    for (int i = 1; i <= n - 1; i++) s.insert(i);
    for (int i = 2; i <= n; i++) {
        int tot = 0;
        for (int j = i; j <= n; j += i) {
            if (a[j]) continue;
            if (tot == 0) {
                tot = *s.begin(); s.erase(s.begin());
            }
            a[j] = tot;
        }
    }
    for (int i = 2; i <= n; i++) printf("%d ", a[i]);
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
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 2000000 + 5;

int n, x, a[maxn];

int main() {
    cin >> n >> x;
    set<int> st, used;
    for (int i = 1; i < (1 << n); i++) st.insert(i);
    int len = 0;
    while (!st.empty()) {
        int tot = *st.begin(); st.erase(st.begin());
        while (used.count(tot ^ x) || tot == x) {
            if (st.empty()) break;
            tot = *st.begin(); st.erase(st.begin());
        }
        if (used.count(tot ^ x) || tot == x) continue;
        a[++len] = tot;
        used.insert(tot);
    }
    printf("%d\n", len);
    for (int i = len; i >= 2; i--) a[i] ^= a[i - 1];
    for (int i = 1; i <= len; i++) {
        printf("%d ", a[i]);
    }
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
const int inf = 1 << 30;
const int maxn = 200000 + 5;

int mpdis[maxn], mpson[maxn];
int qd(int u) {
    if (mpdis[u] != -1) return mpdis[u];
    cout << "d " << u << endl;
    int ans; cin >> ans; 
    return mpdis[u] = ans;
}
int qs(int u) {
    if (mpson[u] != -1) return mpson[u];
    cout << "s " << u << endl;
    int ans; cin >> ans;
    return mpson[u] = ans;
}
void answer(int u) {
    cout << "! " << u << endl;
}

int n;
vector<int> edge[maxn];

int dep[maxn], fa[maxn], siz[maxn], son[maxn], top[maxn];
vector<int> path[maxn];
void dfs(int u, int f) {
    dep[u] = dep[f] + 1; fa[u] = f; siz[u] = 1;
    for (int& v: edge[u]) {
        if (v == f) continue;
        dfs(v, u); siz[u] += siz[v];
        if (siz[v] > siz[son[u]]) son[u] = v;
    }
}
void dfs(int u, int f, int tp) {
    top[u] = tp;
    path[tp].push_back(u);
    if (!son[u]) return ;
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
int qdis(int u, int v) {
    return dep[u] + dep[v] - 2 * dep[qlca(u, v)];
}

int totd;
int solve(int u) {
    int d = path[u].back();
    int d1 = dep[d] - dep[u];
    // int d2 = qd(u);
    int d2 = totd;
    int d3 = qd(d);
    int dy = (d1 + d2 - d3) / 2;
    if (d2 == dy) return path[u][dy];
    int y = path[u][dy];
    totd -= dy + 1;
    return solve(qs(y));
}

int main() {
    ms(mpdis, -1); ms(mpson, -1);
    scanf("%d", &n);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    init();
    totd = qd(1);
    int r = solve(1);
    answer(r);
    return 0;
}
```
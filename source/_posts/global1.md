---
mathjax: true
title: Codeforces Global Round 1 题解
tags:
  - Codeforces
  - Solution
  - Tree
  - Bitmasks
categories:
  - Codeforces
date: 2019-02-08 18:11:38
---

场外云选手，全程智障。

# A Parity

数据范围小，快速幂随便搞一下。

# B Tape

有一段长度为 $m$ 的序列，其中一些位置坏掉了，现在可以用最多 $k$ 个线段覆盖这些坏掉的位置，求最小的覆盖线段总长度。

考虑反面，用 $k$ 段覆盖这些位置，等价于将 $n-k$ 个坏掉位置合并，合并后变成 $k$ 个连续段。

差分一下，拖出来排序，取前 $n-k$ 个。

云选手以为要二分，显然有单调性，但是并不能贪心构造。

# C Meaningless Operations

求 $f(a)=\max_{0 < b < a} \gcd(a \oplus b, a \text{&} b)$。

考虑 $a$ 的二进制表示，如果里面有 $0$，那么 $b$ 相应位置设为 $1$，这样 $a \text{&} b$ 为 $0$， $a \oplus b$ 为 $2^k-1$，显然最大。

所有只需要考虑 $a=2^k-1$ 的情况，只有 $25$ 种，打个表即可。

# D Jongmah

给定一个序列 $a$，$1 \le a_i \le m$，将序列内元素组成一些三元组，满足三元组三个元素为三个连续整数或三个相同整数，求最大能组成多少三元组。

设 $dp[i][j][k]$ 表示考虑到 $i$ 个位置，以第 $i$ 个位置为首组成 $j$ 个连续三元组，第 $i-1$ 个位置为首组成 $k$ 个连续三元组。

然后，观察到组成 $j+3$ 个连续三元组实际上和组成 $j$ 个连续三元组等价，因为可以把横行变成竖列，数目相同，因此只需要考虑 $0 \le j<3, 0 \le k<3$。

转移方程

$$
dp[i][j][k]=\max(dp[i][j][k], dp[i-1][k][h]+{(cnt[i]-j-k-h) \over 3}+j), j+k+h \le cnt[i]
$$

云选手没注意到这个结论。

# E Magic Stones

给定一个序列 $c$，每次操作可以选择除了首尾的一个位置 $i$，执行

$$
c_i=c_{i-1}+c_{i+1}-c_i
$$

判断一下 $c$ 通过这样的操作变成 $t$。

注意到每次这个操作，实际上是交换了左右的差分值，因此只需要判断 $c,t$ 的差分数组排完序是否相同即可。

注意特判首尾位置。

云选手以为要大讨论。

# F Nearest Leaf

顶点编号按照 $dfs$ 序给出一棵带边权的有根树，每次在编号 $[l,r]$ 范围内，询问距离顶点 $v$ 最近的叶子的距离。

离线所有询问，$dfs$ 到一棵子树的时候，相当于子树内所有叶子的距离减去这条边的长度，子树外所有叶子的距离加上这条边的长度，线段树维护即可。

裸题没人做是什么情况？

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
const int mod = 2;
const int maxn = 100000 + 5;

int qpow(int x, int n) {
    int r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}

int b, k;

int main() {
    scanf("%d%d", &b, &k);
    int ans = 0;
    while (k--) {
        int x; scanf("%d", &x);
        ans += x * qpow(b, k) % mod;
    }
    if (ans % 2) puts("odd");
    else puts("even");
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

int n, m, k, a[maxn], b[maxn];

int main() {
    scanf("%d%d%d", &n, &m, &k);
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    sort(a, a + n);
    ll sum = n;
    for (int i = 0; i < n - 1; i++) b[i] = a[i + 1] - a[i] - 1;
    // for (int i = 0; i < n - 1; i++) cout << b[i] << endl;
    sort(b, b + n - 1);
    for (int i = 0; i < n - k; i++) sum += b[i];
    cout << sum << endl;
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
const int maxn = 100000 + 5;

int a, b[30];
int ans[] = { 0,0,1,1,5,1,21,1,85,73,341,89,1365,1,5461,4681,21845,1,87381,1,349525,299593,1398101,178481,5592405,1082401 };

int cal(int x) {
    int ans = 0;
    for (int i = 1; i < x; i++) ans = max(ans, __gcd(i, x - i));
    return ans;
}

int main() {
    for (int i = 1; i < 26; i++) b[i] = (1 << i) - 1;
    int T; scanf("%d", &T);
    while (T--){
        scanf("%d", &a);
        int ans = 0, k = 0;
        for (int i = 1; i < 26; i++) if (a & (1 << i)) k = i + 1;
        ans = (1 << k) - 1;
        for (int i = 1; i < 26; i++) if (a == b[i]) {
            ans = ::ans[i];
        }
        printf("%d\n", ans);
    }
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 1000000 + 5;

int n, m, cnt[maxn], dp[maxn][3][3];

// dp[i][3][3], 

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 0, x; i < n; i++) scanf("%d", &x), cnt[x]++;
    for (int i = 1; i <= m; i++) {
        for (int j = 0; j < 3; j++) for (int k = 0; k < 3; k++) for (int h = 0; h < 3; h++) {
            if (cnt[i] < j + k + h) break;
            dp[i][j][k] = max(dp[i][j][k], dp[i - 1][k][h] + (cnt[i] - j - k - h) / 3 + j);
        }
    }
    cout << dp[m][0][0] << endl;
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
const int mod = 998244353;
const int maxn = 100000 + 5;

int n, c[maxn], t[maxn];

int main() {
    scanf("%d", &n);
    for (int i = 0; i < n; i++) scanf("%d", c + i);
    for (int i = 0; i < n; i++) scanf("%d", t + i);
    if (c[0] != t[0] || c[n - 1] != t[n - 1]) return puts("No"), 0;
    for (int i = n - 1; i > 0; i--) c[i] -= c[i - 1];
    for (int i = n - 1; i > 0; i--) t[i] -= t[i - 1];
    sort(c + 1, c + n); sort(t + 1, t + n);
    for (int i = 1; i < n; i++) if (c[i] != t[i]) return puts("No"), 0;
    puts("Yes");
    return 0;
}
```

## F

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
const int maxn = 500000 + 5;

vector<PII> edge[maxn];
int n, q, tot, in[maxn], out[maxn];
ll dep[maxn];

namespace init {
    void dfs(int u, ll d) {
        in[u] = ++tot; dep[u] = ((int)edge[u].size() == 0 ? d : 1e18);
        for (auto& x: edge[u]) {
            dfs(x.first, d + x.second);
        }
        out[u] = tot;
    }
}

ll tree[maxn << 2], tag[maxn << 2];
void pushup(int rt) {
    tree[rt] = min(tree[rt << 1], tree[rt << 1 | 1]);
}
void pushdown(int rt) {
    if (!tag[rt]) return;
    tree[rt << 1] += tag[rt]; tree[rt << 1 | 1] += tag[rt];
    tag[rt << 1] += tag[rt]; tag[rt << 1 | 1] += tag[rt];
    tag[rt] = 0;
}
void build(int l, int r, int rt) {
    if (l == r) {
        tree[rt] = dep[l]; return;
    }
    int m = (l + r) >> 1;
    build(lson); build(rson);
    pushup(rt);
}
void update(int L, int R, ll x, int l, int r, int rt) {
    if (L <= l && r <= R) {
        tag[rt] += x; tree[rt] += x; return;
    }
    int m = (l + r) >> 1; pushdown(rt);
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    pushup(rt);
}
ll query(int L, int R, int l, int r, int rt) {
    if (L <= l && r <= R) return tree[rt];
    int m = (l + r) >> 1; ll ans = 1e18; pushdown(rt);
    if (L <= m) ans = min(ans, query(L, R, lson));
    if (R > m) ans = min(ans, query(L, R, rson));
    return ans;
}

int l[maxn], r[maxn];
vector<int> que[maxn];

ll allp = 0, ans[maxn];
void dfs(int u) {
    for (int& x: que[u]) {
        // dbg(u, l[x], r[x], query(l[x], r[x], 1, n, 1), allp);
        ans[x] = allp + query(l[x], r[x], 1, n, 1);
    }
    for (auto& x: edge[u]) {
        int v = x.first;
        update(in[v], out[v], -2ll * x.second, 1, n, 1);
        // dbg(in[v], out[v], x.second);
        // for (int i = 1; i <= n; i++) cout << query(i, i, 1, n, 1) << ' '; cout << endl;
        // dbg(in[v], out[v], x.second);
        allp += x.second;
        dfs(v);
        allp -= x.second;
        update(in[v], out[v], 2ll * x.second, 1, n, 1);
    }
}

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 2, p, d; i <= n; i++) {
        scanf("%d%d", &p, &d);
        edge[p].push_back({i, d});
    } init::dfs(1, 0); build(1, n, 1);
    // for (int i = 1; i <= n; i++) cout << query(i, i, 1, n, 1) << endl;
    for (int i = 1, x; i <= q; i++) scanf("%d%d%d", &x, l + i, r + i), que[x].push_back(i);
    dfs(1);
    for (int i = 1; i <= q; i++) printf("%I64d\n", ans[i]);
    return 0;
} 
```
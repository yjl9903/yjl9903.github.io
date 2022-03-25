---
mathjax: true
title: Forethought Future Cup - Elimination Round 题解
tags:
  - Codeforces
  - Solution
  - Tree
  - DP
  - Math
  - Number Theory
  - Data Structure
  - SegTree
categories:
  - Codeforces
date: 2019-04-22 21:11:02
---

# A Love "A"

# B Hate "A"

# C Tree Diameter

给一棵 $100$ 个点的树，询问 $9$ 次，每次询问两个点集之间的最大距离，猜树的直径。

按照线段树类似的分治结构，每层的左区间放在一边询问，右区间放在另一边询问，层数最大为 $9$ 层。

# D Frog Jumping

青蛙在数轴上跳，每次正方向跳 $a$ 步或者反向跳 $b$ 步，记 $f(x)$ 表示青蛙跳在 $[0,x]$ 区间内最多能走到多少个点。

求 $\sum_{i=1}^m f(i)$。

显然当 $x$ 很大时，所有 $\gcd(a,b)$ 的倍数都可以被跳到，贡献可以枚举右端点。

考虑这个分界线可能会在哪，发现 $x \ge a+b$ 时，所有 $\gcd(a,b)$ 的倍数都跳到了。

证明：

对于任意一个可能跳到的位置 $p=ax-by$ 且 $0 \le p \le a+b$。

若 $p \ge b$，则它肯定可以往回跳 $b$。

若 $p \le b$，则它肯定可以往后跳 $a$。

于是，由于初始位置 $0$ 可以被跳到，那么这里面所有位置都可以被跳到。

回到原题，对于在 $[0,a+b)$ 内范围的点对答案贡献，只要暴力模拟出每个位置的第最近位置，若回到一个去过的地方则停止。

对于 $[a+b,m]$ 的贡献，考虑一个公差为 $\gcd(a,b)$ 的等差数列，求和即可。

# E Hot is Cold

给定一个序列，有 $q$ 次操作，每次操作将 $ > x $ 或 $ < x$ 的数变成相反数。

对正值域维护线段树，维护区间赋值和区间翻转标记，以及单纯的区间翻转标记。

以大于为例，大于正的就是区间赋值，大于负的就是一部分区间翻转，一部分区间赋值。

注意打标记的细节。

# F Leaf Partition

将一颗有根树的叶子划分到几个不相交的子图中，子图为包含叶子的最小子图，求方案数。

考虑树形 $dp$，一个点 $u$ 划分为 $3$ 种不相交的状态，$u$ 不在儿子中的任何一个子图，$u$ 在儿子中的子图内，但是只连了一条边，不满足最小子图条件，$u$ 和儿子连成子图，依次为 $dp[u][0,1,2]$。

初始状态为 $dp[leaf][2]=1$，否则 $dp[u][2]=0$。

考虑状态转移，假设对于 $u$ 已经计算了 $dp[u][0,1,2]$，枚举到下一个儿子。

对于 $dp[u][2]$，假如结点 $v$ 连接到 $u$ 上，那么可以和 $dp[u][1]$ 和 $dp[u][2]$ 的状态连接，$v$ 的状态也是 $dp[v][1]$ 和 $dp[v][2]$，假如 $v$ 没有连接到 $u$ 上，那么就是用 $dp[u][2]$ 的状态和 $v$ 不连接上来的状态合并，也就是 $dp[v][0]$ 和 $dp[v][2]$。

对于 $dp[u][1]$ 和 $dp[u][0]$ 也能类似的考虑，注意转移之间的影响。

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

char s[maxn];

int main() {
    cin >> s;
    int c = 0, n = strlen(s);
    for (int i = 0; i < n; i++) if (s[i] == 'a') c++;
    cout << max(0, min(2 * c - 1, n));
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
#include <string>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

char s[maxn];

int main() {
    cin >> s + 1;
    int n = strlen(s + 1);
    int c = 0, tot = 0;
    for (int i = 1; i <= n; i++) if (s[i] == 'a') c++;
    if (c == n) return puts(s + 1), 0;
    for (int i = 1; i <= n; i++) {
        if (s[i] == 'a') tot++;
        if (tot == c && i - tot == n - i) {
            string p, t(s + i + 1);
            for (int j = 1; j <= i; j++) if (s[j] != 'a')p.push_back(s[j]);
            // cout << p << endl << t << endl;
            if (p == t) return s[i + 1] = 0, puts(s + 1), 0;
            else return puts(":("), 0;
        }   
    }
    puts(":(");
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, ans;

int query(int l1, int r1, int l2, int r2) {
    cout << r1 - l1 + 1 << ' ' << r2 - l2 + 1;
    for (int i = l1; i <= r1; i++) cout << ' ' << i;
    for (int i = l2; i <= r2; i++) cout << ' ' << i;
    cout << endl; int ans; cin >> ans;
    return ans;
}
int query(vector<int>& v1, vector<int>& v2) {
    if (v1.empty() || v2.empty()) return 0;
    cout << (int)v1.size() << ' ' << (int)v2.size();
    for (int& x: v1) cout << ' ' << x;
    for (int& x: v2) cout << ' ' << x;
    cout << endl; int ans; cin >> ans;
    return ans;
}

vector<int> v1[20], v2[20];

void get(int l, int r, int d) {
    if (l >= r) return ;
    int m = (l + r) / 2;
    // ans = max(ans, query(l, m, m + 1, r));
    for (int i = l; i <= m; i++) {
        v1[d].push_back(i);
    }
    for (int i = m + 1; i <= r; i++) {
        v2[d].push_back(i);
    }
    get(l, m, d + 1); get(m + 1, r, d + 1); 
}

int main() {
    int T; cin >> T;
    while (T--) {
        for (int i = 0; i <= 9; i++) v1[i].clear(), v2[i].clear();
        cin >> n;
        ans = 0;
        get(1, n, 0);
        for (int i = 0; i < 9; i++) ans = max(ans, query(v1[i], v2[i]));
        cout << "-1 " << ans << endl;
    }    
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
#include <queue>
#include <utility>
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
const int maxn = 200000 + 5;

void exgcd(int a, int b, int& x, int& y) {
    if (b == 0) {
        x = 1; y = 0; return ;
    }
    exgcd(b, a % b, y, x);
    y -= a / b * x;
}
int gcd(int a, int b) { return b ? gcd(b, a % b) : a; }

int m, a, b, dis[maxn], vis[maxn];

int main() {
    cin >> m >> a >> b;
    for (int i = 1; i <= a + b; i++) dis[i] = inf;
    int mx = 0, tot = 0;
    while (true) {
        if (tot >= b) {
            tot -= b;
        } else {
            tot += a;
        }
        if (dis[tot] != inf) break;
        mx = max(mx, tot);
        dis[tot] = mx;
    }
    ll ans = 0;
    for (int i = 0; i < a + b; i++) {
        if (dis[i] == inf) continue;
        ans += max(0, m - dis[i] + 1);
    }
    if (m >= a + b) {
        int g = gcd(a, b);
        int r = m - (a + b) + 1;
        int l = r % g;
        ans += 1ll * (l + r) * ((r - l) / g + 1) / 2;
    }
    cout << ans << endl;
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
#include <assert.h>
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
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
const int inf = 1 << 30;
const int maxn = 200000 + 5;
const int maxv = 100000;

int n, q, a[maxn];

struct Tree { 
    int b[maxn << 2], cov[maxn << 2], rev[maxn << 2];
    void pushdown(int rt) {
        int ls = rt << 1, rs = rt << 1 | 1;
        if (cov[rt]) {
            int t = cov[rt];
            cov[ls] = cov[rs] = b[ls] = b[rs] = t;
            cov[rt] = rev[rt] = 0;
        } else if (rev[rt]) {
            b[ls] = -b[ls]; b[rs] = -b[rs];
            if (cov[ls]) cov[ls] = -cov[ls]; else rev[ls] ^= 1;
            if (cov[rs]) cov[rs] = -cov[rs]; else rev[rs] ^= 1;
            rev[rt] = 0;
        }
    }
    void update1(int L, int R, int x, int l, int r, int rt) {
        if (L <= l && r <= R) {
            b[rt] = x; cov[rt] = x; return ;
        }
        int m = (l + r) / 2; pushdown(rt);
        if (L <= m) update1(L, R, x, lson);
        if (R > m) update1(L, R, x, rson);
    }
    void update2(int L, int R, int l, int r, int rt) {
        if (L <= l && r <= R) {
            b[rt] = -b[rt]; 
            if (cov[rt]) cov[rt] = -cov[rt]; else rev[rt] ^= 1;
            return ;
        }
        int m = (l + r) / 2; pushdown(rt);
        if (L <= m) update2(L, R, lson);
        if (R > m) update2(L, R, rson);
    }
} f1, f2;

int ans[maxn];
void build(int l, int r, int rt) {
    if (l == r) {
        if (f1.b[rt]) ans[l] = f1.b[rt];
        else if (f2.b[rt] == 1) ans[l] = 2;
        else ans[l] = 0;
        return ;
    }
    int m = (l + r) / 2; 
    f1.pushdown(rt); f2.pushdown(rt);
    build(lson); build(rson);
}

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    f2.update1(1, maxv, -1, 1, maxv, 1);
    char op[2]; int x;
    while (q--) {
        scanf("%s%d", op, &x);
        if (op[0] == '<') {
            if (x <= 0) {
                f1.update1(-x + 1, maxv, 1, 1, maxv, 1);
            } else {
                f1.update1(x, maxv, 1, 1, maxv, 1);
                if (x == 1) continue;
                f1.update2(1, x - 1, 1, maxv, 1);
                f2.update2(1, x - 1, 1, maxv, 1);
            }
        } else if (op[0] == '>') {
            if (x >= 0) {
                f1.update1(x + 1, maxv, -1, 1, maxv, 1);
            } else {
                f1.update1(-x, maxv, -1, 1, maxv, 1);
                if (x == -1) continue;
                f1.update2(1, -x - 1, 1, maxv, 1);
                f2.update2(1, -x - 1, 1, maxv, 1);
            }
        }
    }
    build(1, maxv, 1);
    for (int i = 1; i <= n; i++) {
        int t = ans[abs(a[i])];
        if (t == 0) printf("%d ", a[i]);
        else if (t == 1) printf("%d ", abs(a[i]));
        else if (t == -1) printf("%d ", -abs(a[i]));
        else printf("%d ", -a[i]);
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
const int maxn = 200000 + 5;

ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}
ll inv(ll x) {
    return qpow(x, mod - 2);
}
void add(ll& x, ll y) {
    x += y; if (x >= mod) x -= mod;
}
void sub(ll& x, ll y) {
    x -= y; if (x < 0) x += mod;
}
void mul(ll& x, ll y) {
    x = x * y % mod;
}

vector<int> edge[maxn];
int n;

ll dp[maxn][3];
// dp[leaf][2] = 1, otherwise dp[u][2] = 0
void dfs(int u) {
    if (edge[u].empty()) {
        dp[u][2] = 1; return ;
    }
    dp[u][0] = 1;
    int cnt = 0;
    for (int& v: edge[u]) {
        dfs(v);
        dp[u][2] = ((dp[u][1] + dp[u][2]) * (dp[v][2] + dp[v][1]) + dp[u][2] * (dp[v][0] + dp[v][2])) % mod;
        // root's half subgraph and subgraph connect to child's subgraph or child's subgrah
        // root's subgraph dont connect to child's subgraph and no graph
        dp[u][1] = (dp[u][0] * (dp[v][2] + dp[v][1]) + dp[u][1] * (dp[v][0] + dp[v][2])) % mod;
        // root's no graph connect to child's subgraph and half subgraph
        // root's half subgraph dont connect to child's ...
        mul(dp[u][0], dp[v][0] + dp[v][2]);
        // root dont connect to child's ...
    }
    // dbg(u, dp[u][0], dp[u][1], dp[u][2]);
}

int main() {
    scanf("%d", &n);
    for (int i = 2, p; i <= n; i++) {
        scanf("%d", &p); edge[p].push_back(i);
    }
    dfs(1);
    cout << (dp[1][0] + dp[1][2]) % mod;
    return 0;
}
```
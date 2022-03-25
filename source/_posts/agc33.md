---
mathjax: true
title: AtCoder Grand Contest 33 题解
tags:
  - AtCoder
  - Solution
  - K-D Tree
  - Tree
  - Math
  - Greedy
categories:
  - AtCoder
date: 2019-05-04 23:59:46
---

# A Darker and Darker

给定一个 $n\times m$ 的 $01$ 矩阵，求 $01$ 之间的最大曼哈顿距离。

$KD-Tree$ 询问最近点（误

把黑点加进去 $BFS$ 扩展即可。

# B LRUD Game

有 $n\times m$ 的地图，有一个棋子一开始在 $(x,y)$ 处，有两个 `LRDU` 操作序列 $S,T$，两人分别用这两个操作序列移动棋子，在 $i$ 轮，第一个人可以选择是否用 $s$ 的第 $i$ 个操作，第二个人选择是否用 $t$ 的第 $i$ 个操作，第一个人要将棋子移出地图，第二个人组织第一个人，求第一个人是否能获胜。

其实上下左右四个方向是独立的，分成 $4$ 个方向贪心即可，注意边界。

# C Removing Coins

给定一棵无根树，每个结点上有一枚硬币，两人轮流操作。操作是将一个有硬币的结点上所有硬币拿走，然后其他所有结点上的全部硬币向选择的这个点的方向移动一步，判断是否先手必胜。

考虑一个操作，如果选择当前的直径的端点，那么直径长度减一，否则选择任何其他点，都会导致直径长度减二。

因此，我们只需要关注直径的变化即可。每次操作实际上是从 $n$ 个石子里面，要么选 $2$ 个，要么选 $1$ 个。特别地，$2$ 个石子时只会选 $1$ 个。

也就是说，$1$ 是必胜态，$2$ 是必败态，$3$ 必胜态，$4$ 转移到 $2$，$5$ 转移到 $3$ 和 $4$，因此归纳易得，$n$ 模 $3$ 余 $2$ 时是必败态。

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
const int maxn = 1000 + 5;

namespace kdt{
    int rt, cmpd;
    struct node{
        int d[2], mx[2], mn[2], l, r, id;
        bool operator<(const node& b)const{
            return d[kdt::cmpd] < b.d[kdt::cmpd];
        }
    } tree[maxn * maxn];

    inline void pushup(int u, int v){
        node& a = tree[u], & b = tree[v];
        for (int i = 0; i < 2; i++){
            a.mx[i] = max(a.mx[i], b.mx[i]);
            a.mn[i] = min(a.mn[i], b.mn[i]);
        }
    }
    inline int build(int l, int r, int k){
        int m = l + r >> 1; cmpd = k;
        nth_element(tree + l, tree + m, tree + r + 1);
        node& t = tree[m]; t.l = t.r = 0;
        for (int i = 0; i < 2; i++) t.mx[i] = t.mn[i] = t.d[i];
        if (l != m){
            t.l = build(l, m - 1, k ^ 1);
            pushup(m, t.l);
        }
        if (r != m){
            t.r = build(m + 1, r, k ^ 1);
            pushup(m, t.r);
        }
        return m;
    }

    inline ll distance(const node& a, ll x, ll y){
        x -= a.d[0]; y -= a.d[1]; return abs(x) + abs(y);
    }
    inline ll cal(int p, ll x, ll y){ // cut
        ll ans = 0; node& a = tree[p];
        if (x < a.mn[0]) ans += abs(a.mn[0] - x);
        if (x > a.mx[0]) ans += abs(a.mx[0] - x);
        if (y < a.mn[1]) ans += abs(a.mn[1] - y);
        if (y > a.mx[1]) ans += abs(a.mx[1] - y);
        return ans;
    }
		
  	ll ans, x, y;
    inline void query(int p){
        node& t = tree[p];
        ll d0 = distance(t, x, y), dl = inf, dr = inf;
        if (x == t.d[0] && y == t.d[1]) d0 = inf; //cut
        ans = min(ans, d0);
        if (t.l) dl = cal(t.l, x, y);
        if (t.r) dr = cal(t.r, x, y);
        if (dl < dr){
            if (dl < ans) query(t.l);
            if (dr < ans) query(t.r);
        }
        else {
            if (dr < ans) query(t.r);
            if (dl < ans) query(t.l);
        }
    }
    inline int query(int a, int b){
        x = a; y = b; ans = inf;
        query(rt); return ans;
    }
    
    inline int insert(int x, int y, int p){
        node& t = tree[p]; t.l = t.r = 0;
        t.mx[0] = t.mn[0] = t.d[0] = x;
        t.mx[1] = t.mn[1] = t.d[1] = y;
        int now = rt, k = 0;
        while (true){
            pushup(now, p);
            if (tree[now].d[k] <= tree[p].d[k]){
                if (!tree[now].l) return tree[now].l = p;
                now = tree[now].l;
            }
            else {
                if (!tree[now].r) return tree[now].r = p;
                now = tree[now].r;
            }
            k ^= 1;
        }
        return 0;
    }
}
using namespace kdt;

int n, m;
char mp[maxn][maxn];

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%s", mp[i] + 1);
    int tot = 0;
    for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++) if (mp[i][j] == '#') {
        tree[++tot].d[0] = i;
        tree[tot].d[1] = j;
    }
    rt = build(1, tot, 1);
    int ans = 0;
    for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++) if (mp[i][j] == '.') {
        ans = max(ans, query(i, j));
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

int n, m, q;
PII st;
char s[maxn], t[maxn];
int dp[maxn][4];

int main() {
    scanf("%d%d%d%d%d%s%s", &n, &m, &q, &st.first, &st.second, s + 1, t + 1);
    int x = st.first, y = st.second, flag = 0;
    for (int i = 1; i <= q; i++) {
        if (s[i] == 'L') y--;
        if (y == 0) flag = 1;
        if (t[i] == 'R' && y != m) y++;
    }
    y = st.second;
    for (int i = 1; i <= q; i++) {
        if (s[i] == 'R') y++;
        if (y == m + 1) flag = 1;
        if (t[i] == 'L' && y != 1) y--;
    }
    for (int i = 1; i <= q; i++) {
        if (s[i] == 'U') x--;
        if (x == 0) flag = 1;
        if (t[i] == 'D' && x != n) x++;
    }
    x = st.first;
    for (int i = 1; i <= q; i++) {
        if (s[i] == 'D') x++;
        if (x == n + 1) flag = 1;
        if (t[i] == 'U' && x != 1) x--;
    }
    if (flag) puts("NO");
    else puts("YES");
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
const int maxn = 200000 + 5;

int n, dep[maxn];
vector<int> edge[maxn];

void dfs(int u, int f) {
    dep[u] = dep[f] + 1;
    for (int& v: edge[u]) {
        if (v == f) continue;
        dfs(v, u);
    }
}

int main() {
    scanf("%d", &n);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v); edge[v].push_back(u);
    }
    dfs(1, 0);
    int rt, mx = 0;
    for (int i = 1; i <= n; i++) if (dep[i] > mx) mx = dep[i], rt = i;
    dfs(rt, 0);
    mx = 0; for (int i = 1; i <= n; i++) mx = max(mx, dep[i]);
    if (mx % 3 != 2) puts("First");
    else puts("Second");
    return 0;
}
```
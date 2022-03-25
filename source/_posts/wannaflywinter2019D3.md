---
mathjax: true
title: CCPC-Wannafly Winter Camp Day3
date: 2019-01-22 21:38:08
tags:
- Data Structure 
categories:
- 训练
- Wannafly Camp
---

|    rank    | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |
| :--------: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|    9/55    |  5/10  |  .  |  .  |  .  |  O  |  Ø  |  O  |  O  |  .  |  O  |  .  |

<!--more-->

# D 精简改良

Solved by Grunt and XLor.

求出图 $G=(V,E)$ 的一棵生成树，满足路径总长度最大。

生成树状压 $dp$，考虑 $dp[S][i]$ 表示选出了点集 $S$ 后，以 $i$ 为根的生成树的最大权值。

转移方式，枚举子集 $S_1$ ，再枚举子集的子集 $S_2$，将子集 $S_2$ 的生成树与 $S_2$ 在 $S_1$ 内补集的生成树合并。

枚举子集复杂度 $O(3^n)$（注意写法，防止复杂度退化），因为每个元素的归属有 $3$ 种。

时间复杂度：$O(3^nn)$。

# E 最大独立集

Upsolved by XLor.

给一棵树，有 $m$ 次操作，每次将在树上选一个根，然后将上一次操作得到的树接到这棵树上的每一个顶点，得到一颗新树，求这 $m+1$ 棵树的各个最大独立集大小。

第一次最大独立集可以用树形 $dp​$ 容易求解。

之后每次生成新树的时候，应该将每个前一棵树的最大独立集都选出来，这样显然是最优的。也就是当前树上的节点都不能选取，然后选取了 $n$ 次前一棵树的最大独立集。

然后继续考虑这样做的正确性，显然如果前一棵树的根节点被选上了，那么新树的每一个点一定不能选。如果选出来了，可能导致答案不优。

我们继续考虑一种情况，如果一棵树的根节点选不选对这棵树的最大独立集大小没有影响，那么我们不选这个根节点。考虑下一棵树，因为前一棵树的根节点没有选，所以新树中本来的点还可以再可以选出一个最大独立集，这样这棵树的答案一定比选前一棵树根时的答案优。

最后，我们得到了一个贪心的做法：如果上一棵树的树根可以不选（比较选不选时的答案），那么就不选，那么当前答案为当前树的最大独立集 + $n$ 倍的前一棵树的最大独立集大小；如果上一棵树的树根必选，也就是不选时上一棵答案不优，那么当前答案为 $n$ 倍的前一棵树的最大独立集大小。

至此，我们需要一趟树形 $dp$ 得到每棵子树的最大独立集，第二趟 $dp$ 得到以每个点为根时的最大独立集大小，然后递推即可。

# F 小清新数论

Solved by wb.

杜教筛。

# H 排列

Solved by Grunt.

# I 石头剪刀布

Solved by Grunt and XLor.

首先，我们从概率的角度理解问题，而不是计数。

初始状态下，任何一个人的存活概率为 $1$。

对于每次比赛，连一条从客场作战者到主场作战者的边。

进行一次比赛后，主场作战者的存活概率为 $2\over3$，客场作战者的存活概率为 $1 \over 3$。而主场作战者可能不是原位置上的人，而是这个位置所属于的块内的某一个人，这个块内的每个人存活概率都变成了原来的 $2 \over 3$，客场作战者同理。

因此，我们需要维护的是一个联通块，联通块的合并以及每个点到这个块内根节点的权值乘积，带权并查集启发式合并即可。

# 代码

## D

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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

int n, m, g[20][20], ones[1 << 15], tmp0[30], tmp1[30];
ll dp[1 << 15][20];

int main(){
    for (int i = 1; i < (1 << 15); i++) {
        int cnt = 0, x = i;
        while (x) {
            if (x & 1) cnt++;
            x >>= 1;
        }
        ones[i] = cnt;
    }
    ms(dp, -1);
    scanf("%d%d", &n, &m);
    while (m--) {
        int u, v, w; scanf("%d%d%d", &u, &v, &w);
        u--; v--;
        g[u][v] = g[v][u] = w;
    }
    int ss = 1 << n;
    for (int i = 0; i < n; i++) dp[1 << i][i] = 0;
    for (int s = 1; s < ss; s++) {
        for (int s0 = s; s0; s0 = (s0 - 1) & s) {
            int s1 = s ^ s0;
            int l0 = 0, l1 = 0;
            for (int i = 0; i < n; i++) if (s0 & (1 << i)) tmp0[l0++] = i;
            for (int i = 0; i < n; i++) if (s1 & (1 << i)) tmp1[l1++] = i;
            // for (int i = 0; i < n; i++) {
            for (int k0 = 0; k0 < l0; k0++) {
                int i = tmp0[k0];
                if ((s0 & (1 << i)) == 0 || dp[s0][i] == -1) continue;
                // for (int j = 0; j < n; j++) {
                for (int k1 = 0; k1 < l1; k1++) {
                    int j = tmp1[k1];
                    if ((s1 & (1 << j)) == 0 || g[i][j] == 0 || dp[s1][j] == -1) continue;
                    // ll tot = dp[s0][i] + dp[s1][j] + 1ll * g[i][j] * max();
                    dp[s][i] = max(dp[s][i], dp[s0][i] + dp[s1][j] + 1ll * g[i][j] * ones[s1] * (n - ones[s1]));
                    dp[s][j] = max(dp[s][j], dp[s0][i] + dp[s1][j] + 1ll * g[i][j] * ones[s0] * (n - ones[s0]));
                }
            }
        }
    }
    // for (int i = 1; i < ss; i++) {
    //     dbg(i, dp[i][0], dp[i][1], dp[i][2], dp[i][3], dp[i][4]);
    // }
    ll ans = 0;
    for (int i = 0; i < n; i++) ans = max(ans, dp[ss - 1][i]);
    cout << ans;
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

vector<int> edge[maxn];
int n, m, k[maxn];

int dp[maxn][2];
void dfs(int u, int f) {
    int s1 = 0, s2 = 1;
    for (int& v: edge[u]) {
        if (v == f) continue;
        dfs(v, u);
        s1 += max(dp[v][0], dp[v][1]);
        s2 += dp[v][0];
    }
    dp[u][0] = s1; dp[u][1] = s2;
}

int dp2[maxn][2];// greedy, rebuild greedy
void dfs2(int u, int f, int k1, int k2) {
    int s1 = max(k1, k2), s2 = k1 + 1;
    for (int& v: edge[u]) {
        if (v == f) continue;
        s1 += max(dp[v][0], dp[v][1]);
        s2 += dp[v][0];
    }
    dp2[u][0] = s1; dp2[u][1] = s2;
    for (int& v: edge[u]) {
        if (v == f) continue;
        dfs2(v, u, s1 - max(dp[v][0], dp[v][1]), s2 - dp[v][0]);
    }
}

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    for (int i = 1; i <= m; i++) scanf("%d", k + i);
    dfs(1, 0); 
    dfs2(1, 0, 0, 0);
    int ans = max(dp[1][0], dp[1][1]), last = 0; // 0: not choose
    if (dp[1][1] > dp[1][0]) last = 1;
    printf("%d\n", ans);
    for (int i = 1; i <= m; i++) {
        ans = 1ll * ans * n % mod;
        if (last == 0) {
            ans = (ans + max(dp2[k[i]][0], dp2[k[i]][1])) % mod;
            if (dp2[k[i]][1] > dp2[k[i]][0]) last = 1;
        } else last = 0;
        printf("%d\n", ans);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 200000 + 5;

ll qpow(ll x, ll n = mod - 2) {
    ll r = 1;
    while (n) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}

int n, q, pre[maxn], siz[maxn], val[maxn];

int find(int x) { return x == pre[x] ? x : find(pre[x]); }

int main(){
    scanf("%d%d", &n, &q);
    int sum = 1; for (int i = 1; i <= n; i++) sum = 1ll * sum * 3 % mod;
    for (int i = 1; i <= n; i++) pre[i] = i, siz[i] = 1, val[i] = 1;
    ll k1 = qpow(3), k2 = qpow(2);
    int op, x, y;
    while (q--) {
        scanf("%d%d", &op, &x);
        if (op == 1) {
            scanf("%d", &y);
            x = find(x); y = find(y);
            if (siz[y] < siz[x]) {
                val[y] = 1ll * val[y] * qpow(val[x]) % mod * k2 % mod;
                val[x] = 2ll * val[x] % mod * k1 % mod;
                pre[y] = x; siz[x] += siz[y];
            } else {
                val[x] = 2ll * val[x] % mod * qpow(val[y]) % mod;
                val[y] = 1ll * val[y] * k1 % mod;
                pre[x] = y; siz[y] += siz[x];
            }
        } else if (op == 2) {
            int ans = sum;
            while (x != pre[x]) {
                ans = 1ll * ans * val[x] % mod;
                x = pre[x];
            }
            printf("%d\n", 1ll * ans * val[x] % mod);
        }
    }
    return 0;
}
```


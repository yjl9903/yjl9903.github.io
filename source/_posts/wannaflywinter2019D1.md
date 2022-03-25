---
mathjax: true
title: CCPC-Wannafly Winter Camp Day1
date: 2019-01-20 22:06:26
tags:
- Data Structure 
categories:
- 训练
- Wannafly Camp
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| 4/52 |  5/11  |  .  |  O  |  O  |  .  |  Ø  |  O  |  .  |  .  |  .  |  O  |  .  |

<!--more-->

# B 吃豆豆

Solved(?) by Grunt.

假算法。

Div2版本：$dp[i][j][t]$ 表示 $t$ 秒时，到 $(i,j)$ 获得的最多糖果数，答案遍历一下终点位置处的 dp 值即可。

# C 拆拆拆数

Solved by Grunt and wb.

拆出前几个素数即可。

# E 流流流动

Upsolved by XLor.

角谷猜想。除 $1$ 外所有数字 $x$，奇数和 $3x+1$ 连边，偶数和 $x \over 2$ 连边。你需要从 $1,2,\dots,n$ 中选出一些数字，选数字 $i$ 获得价值 $f[i]$ ，选了数字 $i$ 和 $j$ 之间有连边，失去价值 $d[min(i,j)]$，要求最大化价值。

注意到如果 $1$ 没有和 $4$ 连边的话，这个图是没有环的，因为如果有环，那么存在环上的数字 $x$ 不会通过变换回到 $1$，与角谷猜想矛盾（在所给数据范围内，角谷猜想被验证正确）。

因此这个图实际上是一个森林，简单树形 $dp$ 即可。

# F 爬爬爬山

Solved by Grunt.

一个人一开始有能量 $k$，上下山时，海拔上升一米，能量下降一点，海拔下降一米能量，能量上升一点，一开始可以选择将一些山的高度砍掉，砍掉 $l$ 米消耗 $l^2$ 的花费。山与山之间有无向边，询问一个从 $1$ 到 $n$ 的最短路。

能量实际上就类似于势能，经过高度大于 $k + h_1$ 的山要砍掉高度，Dijkstra 即可。

# J 夺宝奇兵

Solved by Grunt and XLor.

$n$ 个人 $m$ 个物品，每个物品属于一个人，每个物品有一个价格，你现在要从这些人中买物品，使得你的物品数严格大于任意一个人，问最小花费。

首先，这个购买个数和花费的不单调，反例：一个人有 $3$ 个价值巨大的物品，剩下十个人每个人一个价值为 $1$ 的物品，所以你要买 $4$ 个物品，花费为 $4$。

因此，枚举购买的件数，计算当前件数的花费。

将所有人的物品价格降序排序，$x$ 轴为每个人，$y$ 轴为每个人的每个物品，我们购买 $k$ 件，实际上是用一条横线把上面一块的所有物品全部切掉。

将所有物品按照在对应人中的序号和权值两个关键字一起排序，后缀和实际上就是被切掉的物品的权值和。

如果切掉的物品件数少于当前枚举的购买件数，还是要把其剩余的前缀中选择最小的买掉。这个用主席树（权值线段树），离散化后维护每段的个数和权值和。

复杂度：$O(n\log^2n)$

PS：根本不需要主席树来可持久化，每次询问都是前缀和，枚举的时候插入一下即可。

# 代码

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
int n, f[maxn], d[maxn];
ll dp[maxn][2];

void dfs(int u, int fa) {
    dp[u][0] = 0; dp[u][1] = f[u];
    for (int& v: edge[u]) {
        if (v == fa) continue;
        dfs(v, u);
        dp[u][0] += max(dp[v][0], dp[v][1]);
        dp[u][1] += max(dp[v][0], dp[v][1] - d[min(u, v)]);
    }
}

int main(){
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", f + i);
    for (int i = 1; i <= n; i++) scanf("%d", d + i);
    for (int i = 2; i <= n; i++) {
        if (i % 2) {
            if (3 * i + 1 > n) continue;
            edge[i].push_back(3 * i + 1);
            edge[3 * i + 1].push_back(i);
        } else {
            edge[i].push_back(i / 2);
            edge[i / 2].push_back(i);
        }
    }
    int ans = 0;
    for (int i = 1; i <= n; i++) {
        if (!dp[i][1]) {
            dfs(i, 0); 
            ans += max(dp[i][0], dp[i][1]);
        }
    }
    cout << ans << endl;
    return 0;
} 
```

## J

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <map>
#include <utility>
#define lson l, m, ls[rt]
#define rson m + 1, r, rs[rt]
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

vector<int> hv[maxn], lsh;
vector<PII> v;
ll suf[maxn];
int n, m, h[maxn];

int gid(int x) {
    return lower_bound(lsh.begin(), lsh.end(), x) - lsh.begin() + 1;
}

int rt[maxn], ls[maxn * 40], rs[maxn * 40], cnt[maxn * 40], tot; ll sum[maxn * 40];
void update(int p, int x, int pre, int l, int r, int& rt) {
    rt = ++tot; ls[rt] = ls[pre]; rs[rt] = rs[pre];
    cnt[rt] = cnt[pre] + 1; sum[rt] = sum[pre] + x;
    if (l == r) return;
    int m = (l + r) >> 1;
    if (p <= m) update(p, x, ls[pre], lson);
    else update(p, x, rs[pre], rson);
}
ll query(int k, int l, int r, int rt) {
    if (cnt[rt] <= k) return sum[rt];
    if (l == r) return 1ll * sum[rt] / cnt[rt] * k;
    int m = (l + r) >> 1;
    if (cnt[ls[rt]] >= k) return query(k, lson);
    else return sum[ls[rt]] + query(k - cnt[ls[rt]], rson);
}

int main(){
    ms(h, -1);
    scanf("%d%d", &n, &m);
    for (int i = 1, a, c; i <= m; i++) {
        scanf("%d%d", &a, &c);
        hv[c].push_back(a);
        lsh.push_back(a);
    }
    sort(lsh.begin(), lsh.end());
    lsh.resize(unique(lsh.begin(), lsh.end()) - lsh.begin());
    int toti = lsh.size();
    for (int i = 1; i <= n; i++) {
        sort(hv[i].begin(), hv[i].end(), [](int x, int y){return x>y;});
        for (int j = 0; j < hv[i].size(); j++) {
            v.push_back({j, hv[i][j]});
        }
    }
    sort(v.begin(), v.end());
    suf[v.size()] = 0;
    for (int i = v.size() - 1; i >= 0; i--) suf[i] = suf[i + 1] + v[i].second;
    for (int i = 0; i < v.size(); i++) {
        if (h[v[i].first] == -1) {
            h[v[i].first] = i;
        }
        update(gid(v[i].second), v[i].second, rt[i], 1, toti, rt[i + 1]);
    }
    for (int i = 1; i <= m; i++) if (h[i] == -1) h[i] = v.size();
    ll ans = suf[0];
    for (int i = 1; i <= m; i++) {
        ll t = suf[h[i - 1]];
        int c = m - h[i - 1];
        if (i > c) {
            t += query(i - c, 1, toti, rt[h[i - 1]]);
        }
        ans = min(ans, t);
    }
    printf("%lld\n", ans);
    return 0;
}
```
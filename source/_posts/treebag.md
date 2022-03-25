---
mathjax: true
title: 树形依赖背包
date: 2019-01-30 19:10:16
tags:
- Tree
categories:
- 树
---

树上的每个节点均有各自的重量和价值，除了容量限制外，还要求选择一个节点时，其父节点也要被选择，求最大价值。

时间复杂度：$O(nm^2)$（其中 $n$ 为树的大小，$m$ 为容量限制）。

# 优化

对于枚举到的每个节点，记录一下其子树大小。

$dp$ 状态转移时，枚举的个数是当前这个子树大小，对应儿子节点的子树的大小，最后更新子树大小。

相当于每次转移都是从节点枚举过的子树，将没有枚举的儿子合并进来。这个过程对应于枚举以这个点为 $lca$ 的所有点对，总体上它枚举了树上所有点对。

时间复杂度：$O(n^2)$。

<!--more-->

# P2014选课

树形依赖背包裸题。

给的图是一个森林，设置一个虚根 $0$，所有树的根连到虚根上，并注意要选择 $m+1$ 个物品。

## 代码

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

vector<int> edge[maxn];
int n, m, v[maxn];

int dp[maxn][maxn], siz[maxn];
void dfs(int u, int f) {
    siz[u] = 1; dp[u][1] = v[u];
    for (int& v: edge[u]) {
        if (v == f) continue;
        dfs(v, u);
        for (int i = siz[u]; i; i--) {
            for (int j = 0; j <= siz[v]; j++) {
                dp[u][i + j] = max(dp[u][i + j], dp[u][i] + dp[v][j]);
            }
        }
        siz[u] += siz[v];
    }
}

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 1, x; i <= n; i++) {
        scanf("%d%d", &x, v + i);
        edge[x].push_back(i);
    }
    dfs(0, -1);
    cout << dp[0][m + 1] << endl;
    return 0;
}
```

# Codeforces 815C Karen and Supermarket

求树形依赖背包 $b$ 块钱买最多的物品，转化为买 $k$ 个物品的最小权值。

每个点除了价格外，还有一个属性优惠价格，要求使用优惠的节点满足依赖关系，不使用打折无需满足依赖关系。

每个物品最多买一次，求最多的数量。

$dp$ 状态多加一维表示是否使用优惠，初始时设置所有 $dp$ 值为无穷大。

不使用优惠的状态，可以满足选一个时，选出的那个不一定是子树的根。

对于使用优惠的状态，可以从子树的选不选打折卷这两种状态转移过来。

## 代码

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
const int maxn = 5000 + 5;

vector<int> edge[maxn];
int n, b, d[maxn], c[maxn];

// min cost of subtree-i buy j things, with discount or not
ll dp[maxn][maxn][2];
int siz[maxn];
void dfs(int u, int f) {
    siz[u] = 1;
    dp[u][0][0] = 0;
    dp[u][1][0] = c[u];
    dp[u][1][1] = c[u] - d[u];
    for (int& v: edge[u]) {
        if (v == f) continue;
        dfs(v, u);
        for (int i = siz[u]; i >= 0; i--) {
            for (int j = 1; j <= siz[v]; j++) {
                dp[u][i + j][0] = min(dp[u][i + j][0], dp[u][i][0] + dp[v][j][0]);
                dp[u][i + j][1] = min(dp[u][i + j][1], dp[u][i][1] + dp[v][j][0]);
                dp[u][i + j][1] = min(dp[u][i + j][1], dp[u][i][1] + dp[v][j][1]);
            }
        }
        siz[u] += siz[v];
    }
}

int main(){
    scanf("%d%d", &n, &b);
    for (int i = 1, x; i <= n; i++) {
        for (int j = 0; j <= n; j++) dp[i][j][0] = dp[i][j][1] = 1ll << 60;
        scanf("%d%d", c + i, d + i);
        if (i == 1) continue;
        scanf("%d", &x);
        edge[x].push_back(i);
    }
    dfs(1, 0);
    int ans = 0;
    for (int i = 1; i <= n; i++) if (min(dp[1][i][0], dp[1][i][1]) <= b) ans = i;
    cout << ans << endl;
    return 0;
}
```

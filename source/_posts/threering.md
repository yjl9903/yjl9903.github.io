---
mathjax: true
title: 三元环计数
date: 2018-12-15 15:32:59
tags:
- Combinatorial
categories:
- 组合数学
---

> 给定一个无向简单图 $G=(V,E)$，$|V|, |E| \le 1e5$。
>
> 问有多少个无序三元组 $(i,j,k)$，满足三个点两两有连边。

# 处理方法

1. 将无向图转化成有向图，对于每条边：
   1. **度数大的点连向度数小的点**；
   2. 度数相同，编号小的点连向编号大的点。

2. 枚举每个点 $u$：
   1. 将 $u$ 相邻的每一个点打上标记；
   2. 枚举 $u$ 相邻的每一个点 $v$ 的相邻点 $w$，如果 $w$ 被打上了标记，那么 $(u,v,w)$ 即为一个要求的三元环。

<!--more-->

# 证明

## 预处理后的图是一个有向无环图

假设图 $G=(V,E)​$ 上存在一条环 $a_1, a_2,\dots,a_m,a_1​$ ，那么
$$
deg(a_1) \ge deg(a_2) \ge \dots \ge deg(a_m) \ge deg(a_1)
$$
因此有
$$
deg(a_1)=deg(a_2)=\dots=deg(a_m)=deg(a_1) \\
a_1<a_2< \dots < a_m < a_1
$$
显然矛盾。

## 枚举的时间复杂度为 $O(n \sqrt{n})$

对于枚举的每一个点 $u$ 。

第一次遍历打标记的时间复杂度为 $O(n+m)$。

第二次遍历，考虑每条边 $(u,v)$ 对复杂度的贡献是 $v$ 的出度，对于每个点 $v$ 的出度 $out(v)$：

1. $out(v) \le \sqrt{m}$ 时，对时间复杂度的总贡献为 $O(m \sqrt{m})$ 。
2. $out(v) > \sqrt{m}$ 时，$out(u) > out(v) > \sqrt{m}$，满足这个条件的边数不超过 $\sqrt{m}$ ，对时间复杂度的总贡献为 $O(m \sqrt{m})$ 。

因此，枚举的时间复杂度为 $O(n \sqrt{n} )$ 。

> 颓废了一个多月，滚回来看看组合QwQ。

# 例题

## HDu6184 Counting Stars

求有多少四元组 $(a,b,c,d)$，满足有一条边是公共边的两个三元环。

将所有三元环抠出来，记录每条边能够成三元环的数量 $cnt$，那么它对答案的贡献为 $C(cnt,2)$。

注意预处理的连边方向！

```C++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <map>
#include <utility>
#define assert(x) do{int a=1,b=0;if(!(x))printf("%d",a/b);}while(0)
#ifdef XLor
  #define dbg(args...) do {cout << "debug -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int maxn = 300000 + 5;

int n, m, u[maxn], v[maxn], deg[maxn], vis[maxn];
vector<int> edge[maxn];

int main(){
    while (~scanf("%d%d", &n, &m)) {
        ms(vis, 0); ms(deg, 0); for (int i = 0; i <= n; i++) edge[i].clear();
        for (int i = 0; i < m; i++) {
            scanf("%d%d", u + i, v + i);
            deg[u[i]]++; deg[v[i]]++;
        }
        for (int i = 0; i < m; i++) {
            if (deg[u[i]] != deg[v[i]]) {
                if (deg[u[i]] < deg[v[i]]) swap(u[i], v[i]);
            } else {
                if (u[i] > v[i]) swap(u[i], v[i]);
            }
            edge[u[i]].push_back(v[i]);
        }
        ll ans = 0;
        map<PII,int> mp;
        for (int i = 1; i <= n; i++) {
            for (int v: edge[i]) vis[v] = i;
            for (int v: edge[i]) {
                for (int u: edge[v]) if (vis[u] == i) {
                    int a[3] = {i, u, v}; sort(a, a + 3);
                    mp[{a[0], a[1]}]++; mp[{a[0], a[2]}]++; mp[{a[1], a[2]}]++;
                }
            }
        }
        for (auto& x: mp) {
            dbg(x.second);
            ans += 1ll * x.second * (x.second - 1) / 2;
        }
        printf("%lld\n", ans);
    }
    return 0;
}
```


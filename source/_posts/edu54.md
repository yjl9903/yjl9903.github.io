---
mathjax: true
title: Educational Round 54 题解
tags:
- Codeforces
- Solution
- Graph
- Tree
- Fenwick Tree
categories:
- Codeforces
- Educational Round
date: 2018-11-13 02:09:12
---

这场题怎么这多锅？题面看起来怎么这么麻烦啊，自闭 T^T。

# A Minimizing the String

字符串删掉至多一个字符时得到的串中，输出字典序最小的那个。

显然必须删一个，从左往右必须单调不减，找到第一个断开的位置或者删除最后一个位置即可。

# B Divisor Subtraction

日常 B 翻车？

一开始觉得找到最小素因子，输出 n / 最小素因子 即可，实际上最小素因子会改变。

在第一次删除最小素因子后，n 变成了一个偶数，这时最小素因子不会再次改变。

$ans=1+\frac{(n-mindivsor)}{2}$。

# C Meme Problem

解一元二次方程，这 BC 位置是不是该换一换啊？

# D Edge Deletion

给一个无向带权联通图，记 $d_i$ 表示 1 到 i 的最短路长度，将图上的边删到至多 $k$ 条，最大化最短路长度不变的点的数量。

感觉上跑一个 dijkstra，然后把出现次数最多的扣出来？挂了，直接在最短路图上 dfs 输出 k 条边就行了？

# E Vasya and a Tree

给一棵树，每个节点初始值为 $0$。

定义 $k-subtree(i)$ 表示 $i$ 的子树中深度不超过 $dep[i] + k$ 的顶点构成的树。

$m$ 次询问将 $d-subtree(v)$ 内所有顶点加上 $x$，输出所有点权。

离线询问，注意到每个顶点的权值只与其祖先有关，所以可以用树状数组，对深度维护的前缀和。

<!--more-->

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 200000 + 5;

char s[maxn]; int n;

int main(){
    scanf("%d%s", &n, s);
    if (n == 2) return printf("%c", min(s[0], s[1])), 0;
    for (int i = 0; i + 1 < n; i++){
        if (s[i] > s[i + 1]){
            puts(s + i + 1); return 0;
        }
        putchar(s[i]);
    }
    return 0;
}
```

## B

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

const int maxp = 1000000 + 5;
int vis[maxp], prime[maxp], tot;
void init(){
    ms(vis, 0);
    for (int i = 2; i < maxp; i++){
        if (!vis[i]) prime[tot++] = i;
        for (int j = 0; j < tot && prime[j] * i < maxp; j++){
            vis[i * prime[j]] = 1;
            if (i % prime[j] == 0) break;
        }
    }
}

ll n, ans = 0;

int main(){
    cin >> n; init();
    if (n % 2 == 0) return cout << n / 2 << endl, 0;
    for (int i = 1; i < tot; i++){
        if (n % prime[i] == 0){
            cout << 1 + (n - prime[i]) / 2 << endl; return 0;
        }
    }
    cout << 1 << endl;
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

int d;

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%d", &d);
        if (d * d >= 4 * d){
            double dt = d * d - 4 * d;
            dt = sqrt(dt);
            printf("Y %.10lf %.10lf\n", (d + dt) / 2, (d - dt) / 2);
        }  
        else puts("N"); 
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
#include <queue>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int maxn = 300000 + 5;

struct node{
    int to, id; ll d;
    node(int a, ll b, int c):to(a), d(b), id(c){}
    bool operator<(const node& b)const{
        return d > b.d;
    }
};
vector<node> edge[maxn];
int n, m, k;

bool vis[maxn]; ll dis[maxn];
void dijkstra(){
    for (int i = 2; i <= n; i++) dis[i] = 1ll << 60;
    dis[1] = 0; 
    priority_queue<node> q;
    q.emplace(1, 0, 0);
    while (!q.empty()){
        node t = q.top(); q.pop();
        if (vis[t.to]) continue;
        vis[t.to] = 1;
        for (auto& x : edge[t.to]){
            int v = x.to;
            if (vis[v]) continue;
            if (dis[v] > dis[t.to] + x.d){
                dis[v] = dis[t.to] + x.d;
                q.emplace(v, dis[v], 0);
            }
        }
    }
}

vector<int> ans;
void dfs(int u){
    for (auto& x : edge[u]){
        if (dis[u] + x.d == dis[x.to] && !vis[x.to]){
            vis[x.to] = 1;
            ans.push_back(x.id);
            dfs(x.to);
        }
    }
}

int main(){
    scanf("%d%d%d", &n, &m, &k);
    for (int i = 1, x, y, w; i <= m; i++){
        scanf("%d%d%d", &x, &y, &w);
        edge[x].emplace_back(y, w, i);
        edge[y].emplace_back(x, w, i);
    }
    dijkstra(); 
    ms(vis, 0); dfs(1);
    printf("%d\n", min(k, (int)ans.size()));
    for (int i = 0; i < min(k, (int)ans.size()); i++) printf("%d ", ans[i]);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int maxn = 300000 + 5;

ll tr[maxn];
inline int lowbit(int x){return x & -x;}
inline void update(int i, int x){
    while (i < maxn) tr[i] += x, i += lowbit(i);
}
inline ll query(int i){
    ll r = 0; while (i) r += tr[i], i -= lowbit(i);
    return r;
}

vector<int> edge[maxn];
vector<PII> que[maxn];
int n, m, tot, in[maxn], out[maxn], dep[maxn];
ll ans[maxn];

void dfs1(int u, int f){
    dep[u] = dep[f] + 1;
    in[u] = ++tot; 
    for (auto& v : edge[u]){
        if (v == f) continue;
        dfs1(v, u);
    }
    out[u] = tot;
}
void dfs(int u, int f){
    for (auto& x : que[u]){
        update(dep[u], x.second);
        update(dep[u] + x.first + 1, -x.second);
    }
    ans[u] = query(dep[u]);
    for (auto& v : edge[u]){
        if (v == f) continue;
        dfs(v, u); 
    }
    for (auto& x : que[u]){
        update(dep[u], -x.second);
        update(dep[u] + x.first + 1, x.second);
    }
}

int main(){
    scanf("%d", &n);
    for (int i = 1, x, y; i < n; i++) {
        scanf("%d%d", &x, &y); 
        edge[x].push_back(y); edge[y].push_back(x);
    } dfs1(1, 0);
    scanf("%d", &m);
    while (m--){
        int v, d, x; scanf("%d%d%d", &v, &d, &x);
        que[v].push_back({d, x});
    }
    dfs(1, 0);
    for (int i = 1; i <= n; i++) printf("%I64d ", ans[i]);
    return 0;
}
```
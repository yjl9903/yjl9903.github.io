---
mathjax: true
title: 树上启发式合并
date: 2018-11-09 00:18:06
tags:
- Tree
categories:
- 树
---

树上启发式合并是用于解决一类树上无修改的子树信息离线查询问题的一种有力工具。

其时间复杂度由轻重链剖分来保证，轻儿子的大小会小于父亲大小的一半，也就是一条链上最多有 $\log$ 数量级的轻儿子。

树上启发式合并，优化自一个 $O(n^2)$ 的想法，对于一个节点，递归计算这棵子树，更新答案，然后消除其所有子树的贡献，防止儿子之间相互影响。

但是，这样做实际上有些答案的贡献是多删除的，应该保留一些贡献不删除，根据树链剖分选择保留重儿子的信息回溯到父亲节点。

之后我们得到了这样的过程，递归进入一个节点，先计算其轻儿子的答案，最后计算重儿子，之后将重儿子的信息上传到父亲，结合这些信息再递归计算这棵子树的轻儿子位置，得到该节点的答案，最后根据递归参数调整是否要上传子树信息。

<!--more-->

# CF600E Lomsat gelral

给一棵有根数，每个点染上颜色，询问每个点的子树中出现次数颜色的颜色值之和（每种颜色只算一次）。

用一个全局变量记录当前子树的答案，注意擦除子树信息时，全局变量的初始化。

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

vector<int> edge[maxn];
int n, c[maxn];
ll ans[maxn], cnt[maxn];

int sz[maxn], son[maxn];
void getsz(int u, int f){
    sz[u] = 1; 
    for (auto& v : edge[u]){
        if (v == f) continue;
        getsz(v, u); sz[u] += sz[v];
        if (sz[v] > sz[son[u]]){
            son[u] = v;
        }
    }
}
ll res = 0; int mx = 0; bool sk[maxn];
void add(int u, int f, int x){
    cnt[c[u]] += x;
    if (x > 0 && cnt[c[u]] >= mx){
        if (cnt[c[u]] > mx) res = 0, mx = cnt[c[u]];
        res += c[u];
    }
    for (auto& v : edge[u]){
        if (v == f || sk[v]) continue;
        add(v, u, x);
    }
}
void dfs(int u, int f, int kp){
    for (auto& v : edge[u]){
        if (v == f || v == son[u]) continue;
        dfs(v, u, 0);
    }
    if (son[u]) dfs(son[u], u, 1), sk[son[u]] = 1;
    add(u, f, 1);
    ans[u] = res;
    if (son[u]) sk[son[u]] = 0;
    if (!kp) add(u, f, -1), res = mx = 0;
}

int main(){
    scanf("%d", &n); for (int i = 1; i <= n; i++) scanf("%d", c + i);
    for (int i = 1; i < n; i++){
        int x, y; scanf("%d%d", &x, &y);
        edge[x].push_back(y); edge[y].push_back(x);
    }
    getsz(1, 0); dfs(1, 0, 0);
    for (int i = 1; i <= n; i++) printf("%I64d ", ans[i]);
    return 0;
}
```

# CF570D Tree Requests

给一棵有根数，每个点有一个字符，询问顶点 $v$ 的子树中深度为 $h$ 的点的字符排列之后能否形成一个回文串。

离线所有询问，记录每个深度的字符出现次数，更新答案时遍历这个询问集合进行更新。

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <set>
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 500000 + 5;

vector<int> edge[maxn];
set<int> que[maxn];
map<int,int> ans[maxn];
char s[maxn];
int n, q, v[maxn], h[maxn];

int sz[maxn], son[maxn], dep[maxn];
void getsz(int u, int f){
    sz[u] = 1; dep[u] = dep[f] + 1;
    for (auto& v : edge[u]){
        if (v == f) continue;
        getsz(v, u); sz[u] += sz[v];
        if (sz[v] > sz[son[u]]) son[u] = v;
    }
}

int sk[maxn], cnt[maxn][30];
bool check(int d){
    int flag = 0;
    for (int i = 0; i < 26; i++){
        if (cnt[d][i] & 1) flag++;
    }
    return flag <= 1;
}
void add(int u, int f, int x){
    cnt[dep[u]][s[u] - 'a'] += x;
    for (auto& v : edge[u]){
        if (v == f || sk[v]) continue;
        add(v, u, x);
    }
}
void dfs(int u, int f, int kp){
    for (auto& v : edge[u]){
        if (v == f || v == son[u]) continue;
        dfs(v, u, 0);
    }
    if (son[u]) dfs(son[u], u, 1), sk[son[u]] = 1;
    add(u, f, 1); sk[son[u]] = 0;

    for (auto& x : que[u]){
        if (check(x)) ans[u][x] = 1;
    }

    if (!kp) add(u, f, -1);
}

int main(){
    scanf("%d%d", &n, &q);
    for (int i = 2, x; i <= n; i++) scanf("%d", &x), edge[x].push_back(i);
    scanf("%s", s + 1);
    for (int i = 1; i <= q; i++){
        scanf("%d%d", v + i, h + i);
        que[v[i]].insert(h[i]);
    }
    getsz(1, 0); dfs(1, 0, 1);
    for (int i = 1; i <= q; i++){
        // printf("%d\n", ans[v[i]][h[i]]);
        if (ans[v[i]][h[i]]) puts("Yes");
        else puts("No");
    }
    return 0;
}
```

# CF1009F Dominant Indices

每个子树维护一个数组，表示距离这个子树根节点为 $d$ 的节点个数，询问每个点的这个数组最大值的位置，如果有很多最大值，回答最浅的那个。

一开始好像写了一个 $O(n \log^3{n})$？信息用 map 存，然后每个子树又遍历一次 $map$，被卡的明明白白。

要的复杂度应该是 $O(n \log^2{n})$，维护每个深度的个数，用一个 set 维护个数最大值、深度最小值。

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <utility>
#include <map>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int mod = 1e9 + 7;
const int maxn = 1000000 + 5;

vector<int> edge[maxn];
int n, ans[maxn], siz[maxn], son[maxn], dep[maxn];

void dfs(int u, int f) {
    dep[u] = dep[f] + 1; siz[u] = 1;
    int m = -1;
    for (int& v: edge[u]) {
        if (v == f) continue;
        dfs(v, u); 
        siz[u] += siz[v];
        if (siz[v] > m) m = siz[v], son[u] = v;
    }
}

int cnt[maxn]; set<PII> st; int sk;
void update(int u, int f, int x) {
    st.erase({-cnt[dep[u]], dep[u]});
    cnt[dep[u]] += x;
    st.insert({-cnt[dep[u]], dep[u]});
    for (int& v: edge[u]) {
        if (v == f || v == sk) continue;
        update(v, u, x);
    }
}
void dfs(int u, int f, int kp) {
    for (int& v: edge[u]) {
        if (v == f || v == son[u]) continue;
        dfs(v, u, 0);
    }
    if (son[u]) dfs(son[u], u, 1), sk = son[u];
    update(u, f, 1); sk = 0;
    ans[u] = st.begin()->second - dep[u];
    if (!kp) update(u, f, -1);
}

int main(){
    scanf("%d", &n);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    dfs(1, 0); dfs(1, 0, 1);
    for (int i = 1; i <= n; i++) printf("%d\n", ans[i]);
    return 0;
}
```
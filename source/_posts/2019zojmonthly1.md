---
mathjax: true
title: ZOJ Monthly, January 2019 题解
date: 2019-01-19 22:28:17
tags:
- Graph
- Data Structure 
categories:
- 训练
---

# A Little Sub and Pascal's Triangle

求杨辉三角第 $n$ 层的奇数个数。

组合数 $n \choose k$ 为奇数，充要条件为 $n \text{ AND } k = k$。

答案为 $2^p$，其中 $p$ 为 $n-1$ 二进制表示中 $1$ 的个数。

# G Little Sub and Tree

# I Little Sub and Isomorphism Sequences

两个序列同构的条件为：

1. 长度相同；

2. 每个数的出现次数相同。

维护两个操作：

1. 单点覆盖；

2. 询问全局两个同构的不同连续子序列的长度最大值，即满足 
$$ 
a_{i},a_{i+1},\dots,a_{i+k-1} \\ 
a_{j},a_{j+1},\dots,a_{j+k-1} 
$$
这两个不同的序列同构，询问 $k$ 最大值。 

想一想发现，显然两个子序列同构肯定是共用 $k-1$ 个位置，那么维护一下每个数出现的最左边和最右位置，询问的时候 $set$ 里面维护最大距离即可。

证明一下：

考虑两个同构串共用了 $m$ 个位置，对于两个串的非共用部分，总能找到一对相同的数字，以这两个数字为端点，显然可以构造出一组更长的同构串，因此最优情况就是 $k-1$ 位置都共用了。

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
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

ll n, two[100];

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%lld", &n);
        n--; int cnt = 0;
        for (int i = 0; i < 63; i++) if ((1ll << i) & n) cnt++;
        printf("%lld\n", 1ll << cnt);
    }
    return 0;
}
```

## G

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
int n, deg[maxn], tag[maxn];

int dfs(int u, int f) {
    if (tag[u]) return 0;
    if (deg[u] == 2) {
        for (int& v: edge[u]) {
            if (v == f) continue;
            if (dfs(v, u)) {
                tag[u] = 1;
                return 1;
            } else return 0;
        }
    }
    return tag[u] = 1; 
}

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%d", &n);
        for (int i = 2, u, v; i <= n; i++) {
            scanf("%d%d", &u, &v);
            edge[u].push_back(v);
            edge[v].push_back(u);
            deg[u]++; deg[v]++;
        }
        vector<int> leaf, ans; int cnt = 0;
        for (int i = 1; i <= n; i++) if (deg[i] == 1) leaf.push_back(i); else if (deg[i] == 2) cnt++;
        if (cnt + 2 == n) {
            printf("1\n%d\n", leaf[0]);
            for (int i = 1; i <= n; i++) {
                deg[i] = tag[i] = 0; edge[i].clear();
            }
            continue;
        }
        for (int& u: leaf) {
            if (dfs(edge[u].front(), u)) {

            } else {
                ans.push_back(u);
            }
        }
        printf("%d\n", ans.size());
        for (int i = 0; i < ans.size(); i++) {
            if (i) putchar(' ');
            printf("%d", ans[i]);
        }
        puts("");
        for (int i = 1; i <= n; i++) {
            deg[i] = tag[i] = 0; edge[i].clear();
        }
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
#include <set>
#include <map>
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int mod = 1e9 + 7;
const int maxn = 500000 + 5;

int n, q, a[maxn], l[maxn], r[maxn];
set<PII> st;
set<int> pos[maxn];
map<int,int> mp; int tot = 0;
int gid(int x) {
    if (mp[x]) return mp[x];
    return mp[x] = ++tot;
}
void insert(int x, int p) {
    x = gid(x);
    if (l[x] == 0) {
        l[x] = r[x] = p;
        // st.insert({x, p, p});
        st.insert({0, x});
        pos[x].clear();
        pos[x].insert(p);
    } else {
        // st.erase({x, l[x], r[x]});
        st.erase({r[x] - l[x], x});
        l[x] = min(l[x], p);
        r[x] = max(r[x], p);
        st.insert({r[x] - l[x], x});
        // st.insert({x, l[x], r[x]});
        pos[x].insert(p);
    }
}

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%d%d", &n, &q);
        st.clear(); tot = 0; mp.clear(); ms(l, 0);
        for (int i = 1; i <= n; i++) {
            scanf("%d", a + i);
            insert(a[i], i);
        }
        int op, x, y;
        while (q--) {
            scanf("%d", &op);
            if (op == 1) {
                scanf("%d%d", &x, &y);
                int t = gid(a[x]);
                pos[t].erase(x);
                if (!pos[t].empty()) {
                    st.erase({r[t] - l[t], t});
                    // st.erase({t, l[t], r[t]});
                    l[t] = *(pos[t].begin());
                    r[t] = *(pos[t].rbegin());
                    st.insert({r[t] - l[t], t});
                }
                a[x] = y;
                insert(y, x);
            } else if (op == 2) {
                PII t = *st.rbegin();
                if (t.first) printf("%d\n", t.first);
                else puts("-1");
            }
        }
    }
    return 0;
}
```
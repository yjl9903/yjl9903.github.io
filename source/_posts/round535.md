---
mathjax: true
title: Codeforces Round 535 题解
tags:
- Codeforces
- Solution
- Data Structure 
- SegTree 
categories:
- Codeforces
date: 2019-01-25 01:41:31
---

$div3$ 恢复信心失败。

# A Two distinct points

# B Divisors of Two Integers

给了一个多重集合，存放着两个正整数 $x$ 和 $y$ 的所有因子，让你恢复出这两个数字。

显然最大值是一个解，对其质因数分解，从多重集合中删除，得到剩下数字中的最大值为另外一个解。

# C Nice Garland

给一个只包含字母 $RGB$ 的串，现在要求这个串 $s$ 满足
$$
\forall i, j \text{ 满足 } s_i=s_j,|i-j|\mod 3=0
$$
你可以修改一些位置使得上条件满足，求最小修改数和修改方案。

显然，这个串的前 $3$ 个位置只有 $6$ 种情况，后面部分一直重复这个前缀，枚举前缀即可。

# D Diverse Garland

给一个只包含字母 $RGB$ 的串，现在要求这个串 $s$ 满足所有相邻位置字母不同。

你可以修改一些位置使得上条件满足，求最小修改数和修改方案。

考虑 $dp[i][3]$ 表示前 $i$ 个位置，最后一个为 $RGB$ 的最小修改数，跑一遍 $dp$，再倒着转移把方案构造出来。

菜鸡选手不会写倒着重构 $dp$。

# E Array and Segments

给一个序列 

$$ 
a_{1},a_{2},\cdot\cdot\cdot,a_{n} 
$$

现在有 $q$ 次询问，每次将区间 $[l,r]$ 所有位置都 $-1$，现在你需要从询问中选择一些执行，使得序列极差最大，即最大化 $\max a_i - \min a_i$。

对于一类求极差的问题，可以转化为枚举一个，快速计算另外一个。

因此，我们枚举最大值的位置，将所有不包含这个位置的线段全部执行，询问整个区间的最小值，这部分线段树更新和查询即可。

但是此时复杂度为 $O(nm\log n)$。

优化一下，我们可以用类似于双指针的思想，枚举每一个位置时，更新一些询问和撤销一些询问，因此每个询问最多被更新 $2$ 次。

时间复杂度 $O((n+m)\log n)$。

菜鸡选手并不知道极差的套路。

# F MST Unification

给一个无向带权连通图，现在你可以将某些边权 $+1$ ，求最小的修改次数使得修改后的最小生成树唯一，且最小生成树权值不增加。

已经求出一棵最小生成树，当且仅当一些非树边的权值等于其对应树上路径的最大边权时，可以将路径上的最大边断掉换成这个非树边。

所以问题转化为快速求得树上一条路径的最大边权，在树上建一棵主席树即可。

菜鸡选手没有思维，只会无脑拍数据结构。

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

int l1, r1, l2, r2;

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%d%d%d%d", &l1, &r1, &l2, &r2);
        if (l1 != r2)
            printf("%d %d\n", l1, r2);
        else if (l1 != l2)
            printf("%d %d\n", l1, l2);
        else if (r1 != l2)
            printf("%d %d\n", r1, l2);
        else 
            printf("%d %d\n", r1, r2);
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
#include <vector>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

int n, d[maxn];
multiset<int> st;

int main(){
    scanf("%d", &n); 
    for (int i = 1, x; i <= n; i++) {
        scanf("%d", &x); st.insert(x);
    }
    int ansy = *st.rbegin();
    for (int i = 1; i * i <= ansy; i++) {
        if (ansy % i) continue;
        st.erase(st.find(i));
        if (i * i != ansy) st.erase(st.find(ansy / i));
    }
    cout << *st.rbegin() << ' ' << ansy << endl;
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
const int mod = 1e9 + 7;
const int maxn = 200000 + 5;

int n; char s[maxn], t[maxn];
char p[5] = "BGR";

int main(){
    scanf("%d%s", &n, s);
    int mn = 1e9;
    do {
        int cnt = 0;
        for (int i = 0; i < n; i++) if (s[i] != p[i % 3]) cnt++;
        if (cnt < mn) {
            mn = cnt;
            for (int i = 0; i < n; i++) t[i] = p[i % 3];
        }
    } while (next_permutation(p, p + 3));
    printf("%d\n%s", mn, t);
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
const int maxn = 200000 + 5;

char p[5] = "RGB";
int n, dp[maxn][3]; char s[maxn], t[maxn];

int main(){
    scanf("%d%s", &n, s + 1);
    for (int i = 1; i <= n; i++) {
        dp[i][0] = min(dp[i - 1][1], dp[i - 1][2]);
        dp[i][1] = min(dp[i - 1][0], dp[i - 1][2]);
        dp[i][2] = min(dp[i - 1][0], dp[i - 1][1]);
        if (s[i] == 'R') {
            dp[i][1]++; dp[i][2]++;
        } else if (s[i] == 'G') {
            dp[i][0]++; dp[i][2]++;
        } else {
            dp[i][0]++; dp[i][1]++;
        } 
        dbg(dp[i][0], dp[i][1], dp[i][2]);
    }
    int ans = min(min(dp[n][0], dp[n][1]), dp[n][2]);
    for (int k = 0; k < 3; k++) {
        if (dp[n][k] != ans) continue;
        char last = p[k]; 
        t[n] = p[k];

        for (int i = n - 1; i >= 1; i--) {
            if (t[i + 1] == 'R') {
                dbg(dp[i + 1][0], dp[i][1], dp[i][2], s[i]);
                if (dp[i][1] + (s[i + 1] == 'R' ? 0 : 1) == dp[i + 1][0]) t[i] = 'G';
                else if (dp[i][2] + (s[i + 1] == 'R' ? 0 : 1) == dp[i + 1][0]) t[i] = 'B';
                else {
                    // cout << "???\n";
                }
            } else if (t[i + 1] == 'G') {
                dbg(dp[i + 1][1], dp[i][0], dp[i][2], s[i]);
                if (dp[i][0] + (s[i + 1] == 'G' ? 0 : 1) == dp[i + 1][1]) t[i] = 'R';
                else if (dp[i][2] + (s[i + 1] == 'G' ? 0 : 1) == dp[i + 1][1]) t[i] = 'B';
                else {
                    // cout << "???\n";
                }
            } else {
                dbg(dp[i + 1][2], dp[i][0], dp[i][1], s[i]);
                if (dp[i][0] + (s[i + 1] == 'B' ? 0 : 1) == dp[i + 1][2]) t[i] = 'R';
                else if (dp[i][1] + (s[i + 1] == 'B' ? 0 : 1) == dp[i + 1][2]) t[i] = 'G';
                else {
                    // cout << "???\n";
                }
            }
        }

        int flag = 1;
        for (int i = 1; i < n; i++) {
            if (t[i - 1] == t[i]) {
                flag = 0; break;
            }
        }
        if (flag) break;
        // cout << t + 1 << endl;
    }
    printf("%d\n%s", ans, t + 1);
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
#include <queue>
#include <utility>
#include <functional>
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
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

int n, m, a[maxn], mn[maxn << 2], tag[maxn << 2];
PII p[maxn];
vector<int> mk[maxn];

void pushup(int rt) {
    mn[rt] = min(mn[rt << 1], mn[rt << 1 | 1]);
}
void pushdown(int rt) {
    if (!tag[rt]) return;
    mn[rt << 1] += tag[rt]; mn[rt << 1 | 1] += tag[rt];
    tag[rt << 1] += tag[rt]; tag[rt << 1 | 1] += tag[rt];
    tag[rt] = 0;
}
void build(int l, int r, int rt) {
    if (l == r) {
        mn[rt] = a[l]; return;
    }
    int m = l + r >> 1;
    build(lson); build(rson);
    pushup(rt);
}
void update(int L, int R, int x, int l, int r, int rt) {
    if (L <= l && r <= R) {
        mn[rt] += x; tag[rt] += x;
        return;
    }
    int m = l + r >> 1; pushdown(rt);
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    pushup(rt);
}
int query(int i, int l, int r, int rt) {
    if (l == r) return mn[rt];
    int m  = l + r >> 1; pushdown(rt);
    if (i <= m) return query(i, lson);
    else return query(i, rson);
}

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    build(1, n, 1);
    for (int i = 1; i <= m; i++) {
        scanf("%d%d", &p[i].first, &p[i].second);
        mk[p[i].first].push_back(p[i].second);
        update(p[i].first, p[i].second, -1, 1, n, 1);
    }
    int ans = 0, ansp = 1, l = 1, r = 0;
    priority_queue<PII, vector<PII>, greater<PII> > pq;
    for (int i = 1; i <= n; i++) {
        while (!pq.empty()) {
            PII t = pq.top();
            if (t.first < i) {
                pq.pop(); update(t.second, t.first, -1, 1, n, 1);
            } else break;
        }
        for (int& x: mk[i]) {
            pq.push({x, i}); update(i, x, 1, 1, n, 1);
        }
        int t = query(i, 1, n, 1) - mn[1];
        if (ans < t) ans = t, ansp = i;
    }
    printf("%d\n", ans);
    vector<int> v;
    for (int i = 1; i <= m; i++) {
        if (ansp > p[i].second || ansp < p[i].first) v.push_back(i);
    }
    printf("%d\n", v.size()); for (int& x: v) printf("%d ", x);
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
#include <queue>
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
#define lson l, m, ls[rt]
#define rson m + 1, r, rs[rt]
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int mod = 1e9 + 7;
const int maxn = 200000 + 5;

struct node {
    int u, v, w;
    bool operator<(const node& b)const{
        return w > b.w;
    }
};

vector<int> lsh;
int gid(int x) {
    return lower_bound(lsh.begin(), lsh.end(), x) - lsh.begin() + 1;
}

int n, m, pre[maxn];

vector<PII> edge[maxn];
int find(int x) { return x == pre[x] ? x : pre[x] = find(pre[x]); }

int tot, root[maxn], ls[maxn * 40], rs[maxn * 40], a[maxn * 40];
void update(int x, int pre, int l, int r, int& rt) {
    rt = ++tot; ls[rt] = ls[pre]; rs[rt] = rs[pre]; a[rt] = a[pre] + 1;
    if (l == r) return;
    int m = l + r >> 1;
    if (x <= m) update(x, ls[pre], lson);
    else update(x, rs[pre], rson);
}
int query(int pre, int l, int r, int rt) {
    if (l == r) return l;
    int m = l + r >> 1;
    // dbg(a[pre], a[rt]);
    if (a[rs[rt]] - a[rs[pre]]) return query(rs[pre], rson);
    else return query(ls[pre], lson);
}

int siz[maxn], dep[maxn], fa[maxn], son[maxn], top[maxn];
void dfs(int u, int f) {
    dep[u] = dep[f] + 1; siz[u] = 1; fa[u] = f;
    int m = -1;
    for (auto& x: edge[u]) {
        int v = x.first;
        if (v == f) continue;
        update(gid(x.second), root[u], 1, lsh.size(), root[v]);
        dfs(v, u);
        siz[u] += siz[v];
        if (siz[v] > m) son[u] = v, m = siz[v];
    }
}
void dfs(int u, int f, int tp) {
    top[u] = tp;
    if (!son[u]) return;
    dfs(son[u], u, tp);
    for (auto& x: edge[u]) {
        int v = x.first;
        if (v == f || v == son[u]) continue;
        dfs(v, u, v);
    }
}
int qlca(int u, int v) {
    while (top[u] != top[v]) {
        if (dep[top[u]] < dep[top[v]]) swap(u, v);
        u = fa[top[u]];
    }
    return dep[u] < dep[v] ? u : v;
}

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) pre[i] = i;
    priority_queue<node> pq; vector<node> v;
    for (int i = 1, u, v, w; i <= m; i++) {
        scanf("%d%d%d", &u, &v, &w);
        pq.push({u, v, w}); lsh.push_back(w);
    }
    sort(lsh.begin(), lsh.end());
    lsh.resize(unique(lsh.begin(), lsh.end()) - lsh.begin());
    while (!pq.empty()) {
        node t = pq.top(); pq.pop();
        int x = find(t.u), y = find(t.v);
        if (x == y) { 
            v.push_back(t);
            continue; 
        }
        pre[y] = x; 
        edge[t.u].push_back({t.v, t.w});
        edge[t.v].push_back({t.u, t.w});
    }
    dfs(1, 0); dfs(1, 0, 1);
    int ans = 0;
    for (auto& x: v) {
        int lca = qlca(x.u, x.v);
        if (x.u == lca) {
            if (query(root[lca], 1, lsh.size(), root[x.v]) == gid(x.w)) ans++;
            continue;
        } else if (x.v == lca) {
            if (query(root[lca], 1, lsh.size(), root[x.u]) == gid(x.w)) ans++;
            continue;
        }
        // dbg(x.u, x.v, x.w, lca);
        int a = query(root[lca], 1, lsh.size(), root[x.u]);
        int b = query(root[lca], 1, lsh.size(), root[x.v]);
        // dbg(a, b);
        if (max(a, b) == gid(x.w)) ans++;
    }
    cout << ans << endl;
    return 0;
}
```
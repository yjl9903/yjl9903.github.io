---
mathjax: true
title: Educational Round 62 题解
tags:
  - Codeforces
  - Solution
  - Data Structure
  - SegTree
  - Graph
categories:
  - Codeforces
  - Educational Round
date: 2019-03-25 20:42:44
---

# A Detective Book
 
# B Good String

# C Playlist

有 $n$ 首歌，每首歌有长度 $t_i$ 和好听度 $b_i$，选出至多 $k$ 首歌，他们的权值是好听度的最小值乘时长和，求最大权值。

按照好听度排序，倒着维护长度的前 $k$ 大。

# D Minimum Triangulation

# F Extending Set of Points

一个点集，若有点 $(x_1,y_1),(x_1,y_2),(x_2,y_1)$ 则也会有点 $(x_2,y_2)$。

给定一串加点或删点操作序列，若之前出现过当前点则为删点，否则为加点，求每次操作后的点数。

将两个维度拆开，加入一个点 $(x,y)$ 意味着编号为 $x$ 的白点连向了编号为 $y$ 的黑点，否则为删边。

每次询问就是询问，一个联通块内黑点数乘白点数，对所有联通块求和。

于是，我们要维护一个带删边的并查集，以及集合大小信息。

线段树分治 + 带撤销并查集。

线段树分治，即对时间维建立线段树，将每个点的贡献放到线段树的结点上打 $\log n$ 个标记。

最后，分治时统计当前区间的贡献，这个地方需要有并查集的撤销操作。

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

int n, a[maxn]; 

int main() { 
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    int mx = a[1], ans = 0;
    for (int i = 1; i <= n; i++) {
        mx = max(a[i], mx);
        if (mx == i) ans++;
        // if (mx == i) return printf("%d", i), 0;
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

char s[maxn]; int n;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%s", &n, s + 1);
        int ans = n;
        for (int i = 1; i <= n; i++) if (s[i] == '>') {ans = min(ans, i - 1); break;}
        reverse(s + 1, s + n + 1);
        for (int i = 1; i <= n; i++) if (s[i] == '<') {ans = min(ans, i - 1); break;}
        printf("%d\n", ans);
    }
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
#include <set>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

int n, k;
PII a[maxn];

int main() {
    scanf("%d%d", &n, &k);
    for (int i = 1; i <= n; i++) {
        scanf("%d%d", &a[i].first, &a[i].second);
    }
    sort(a + 1, a + 1 + n, [](PII a, PII b) {
        if (a.second == b.second) return a.first > b.first;
        return a.second < b.second;
    });
    ll ans = 0, sum = 0; 
    multiset<ll> st;
    for (int i = n; i >= 1; i--) {
        if ((int)st.size() == k) {
            if (a[i].first > *st.begin()) {
                sum -= *st.begin();
                st.erase(st.begin());
                sum += a[i].first;
                st.insert(a[i].first);
            }
        } else {
            st.insert(a[i].first);
            sum += a[i].first;
        }
        ans = max(ans, sum * a[i].second);
    }
    cout << ans << endl;
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
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n;

int main() {
    cin >> n;
    ll ans = 0;
    for (int i = 3; i <= n; i++) ans += 1ll * i * (i - 1); 
    cout << ans << endl;
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
#include <map>
#include <utility>
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
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

int pre[maxn << 1], siz[maxn << 1], xcnt[maxn << 1], ycnt[maxn << 1];
int find(int x) {
    while (x != pre[x]) x = pre[x]; return x;
}
ll sum = 0;
vector<PII> sta;
bool join(int x, int y) {
    // dbg(x, y);
    x = find(x); y = find(y);
    if (x == y) return 0;
    sum -= 1ll * xcnt[x] * ycnt[x]; sum -= 1ll * xcnt[y] * ycnt[y];
    if (siz[x] > siz[y]) swap(x, y);
    pre[x] = y; siz[y] += siz[x];
    xcnt[y] += xcnt[x]; ycnt[y] += ycnt[x];
    sum += 1ll * xcnt[y] * ycnt[y];
    sta.push_back({x, y});
    return 1;
}
void undo() {
    PII tp = sta.back(); sta.pop_back();
    int x = tp.first, y = tp.second;
    pre[x] = x; siz[y] -= siz[x];
    sum -= 1ll * xcnt[y] * ycnt[y];
    xcnt[y] -= xcnt[x]; ycnt[y] -= ycnt[x];
    sum += 1ll * xcnt[x] * ycnt[x]; sum += 1ll * xcnt[y] * ycnt[y];
}

vector<PII> nodes[maxn << 2];
void update(int L, int R, PII x, int l, int r, int rt) {
    if (L <= l && r <= R) {
        nodes[rt].push_back(x); return ;
    }
    int m = (l + r) >> 1;
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
}

ll ans[maxn];
void solve(int l, int r, int rt) {
    int cnt = 0;
    // dbg(l, r);
    // for (auto& x: nodes[rt]) dbg(x.first, x.second);
    for (auto& x: nodes[rt]) if (join(x.first, x.second)) cnt++;
    if (l == r) {
        ans[l] = sum;
        for (int i = 0; i < cnt; i++) undo();
        return ;
    }
    int m = (l + r) >> 1;
    solve(lson); solve(rson);
    for (int i = 0; i < cnt; i++) undo();
}

int n, x[maxn], y[maxn]; 
map<PII,int> mp;

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= 2 * n; i++) {
        pre[i] = i; siz[i] = 1;
        xcnt[i] = (i <= n); ycnt[i] = (i > n);
    }
    vector<int> xx, yy;
    for (int i = 1; i <= n; i++) {
        scanf("%d%d", x + i, y + i);
        xx.push_back(x[i]); yy.push_back(y[i]);
    }
    sort(xx.begin(), xx.end()); sort(yy.begin(), yy.end());
    xx.resize(unique(xx.begin(), xx.end()) - xx.begin());
    yy.resize(unique(yy.begin(), yy.end()) - yy.begin());
    for (int i = 1; i <= n; i++) {
        x[i] = lower_bound(xx.begin(), xx.end(), x[i]) - xx.begin() + 1;
        y[i] = lower_bound(yy.begin(), yy.end(), y[i]) - yy.begin() + 1 + n;
    }
    for (int i = 1; i <= n; i++) {
        if (!mp.count({x[i], y[i]}) || mp[{x[i], y[i]}] <= 0) mp[{x[i], y[i]}] = i;
        else {
            update(mp[{x[i], y[i]}], i - 1, {x[i], y[i]}, 1, n, 1);
            mp[{x[i], y[i]}] = -1;
        }
    }
    for (auto& x: mp) if (x.second > 0) update(x.second, n, x.first, 1, n, 1);
    solve(1, n, 1);
    for (int i = 1; i <= n; i++) printf("%I64d ", ans[i]);
    return 0;
}
```
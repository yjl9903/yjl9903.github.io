---
mathjax: true
title: Educational Round 55 题解
tags:
- Codeforces
- Solution
- Graph
- Network-Flow
- DP
categories:
- Codeforces
- Educational Round
date: 2019-03-20 20:56:31
---

# A Vasya and Book

# B Vova and Trophies

给定一个 $01$ 串，求交换某一对字符后，最长的连续 $1$ 串。

只能交换一次，遍历时维护一下前一次的线段长度和端点。

注意判一下在连续 $1$ 串后面补了一个 $1$。

# C Multi-Subject Competition

有 $n$ 个人，$m$ 种学科，每个人擅长学科 $s_i$，能力值为 $r_i$。

求派出一些人，满足每种学科擅长人数相同时的，总能力值最大值。

每种学科中的人按能力值大小排序，枚举每个学科的参与人数，遍历时维护一个前缀和即可。

注意写的时候复杂度不要退化。

# D Maximum Diameter Graph

要求构造 $n$ 个点，满足每个点 $i$ 度数不超过 $a_i$，求最大的图的直径（最短路的最大值）。

显然只需要扣出一棵树即可，满足树的直径最大。

进一步，如果度数都比 $2$ 大，肯定连一条链最优。

但是，考虑到有 $1$ 度结点，对于前两个接在头尾两处，剩余的接在其他点上。

# E Increasing Frequency

给定一个序列 $a$ 和整数 $c$，现在你可对一个区间 $[l,r]$ 内的所有数加上 $x$，求序列内最多有 $c$。

显然就是找一段区间的众数，加上区间外 $c$ 的出现次数。

预处理 $c$ 个数的前缀和 $pre$。

枚举每一种数值 $x$，对于第 $i$ 个 $x$，记为 $x_i$，当前的答案为

$$
\max_{j=1\dots i}(pre[n]-pre[x_i]+pre[x_j-1]+i-j+1) \\
= pre[n]-pre[x_i] + i + 1 + \max_{j=1\dots i}(pre[x_j-1]-j)
$$

后面一块可以遍历时维护出来。

# G Petya and Graph

给定一个 $n$ 个点 $m$ 条边简单无向图，求权值最大的一个子图，权值为边权和减去点权和。

注意到 $n,m \le 1000$ 和题目定义的权值，考虑最大权闭合子图模型。

对所有边建一个点，建立超级源点到每条边上，容量为边权，并将所有点连向一个超级汇点，容量为点权。

对于，一条边它的依赖关系其实就是相邻的两个点，对应连边。

跑一个最大权闭合子图即可。

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
const int maxn = 100000 + 5;

int n, x, y, d;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        cin >> n >> x >> y >> d;
        if (abs(x - y) % d) {
            int ans = 2e9;
            if ((y - 1) % d == 0) {
                ans = (y - 1) / d + (x - 1) / d;
                if ((x - 1) % d) ans++;
            } 
            if ((n - y) % d == 0) {
                int tot = (n - y) / d + (n - x) / d;
                if ((n - x) % d) tot++;
                ans = min(ans, tot);
            }
            if (ans == 2e9) puts("-1");
            else printf("%d\n", ans);
        } else {
            printf("%d\n", abs(x - y) / d);
        }
    }
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
const int maxn = 100000 + 5;

int n; char s[maxn];

int main() {
    cin >> n >> s + 1;
    int c = 0;
    for (int i = 1; i <= n; i++) if (s[i] == 'G') c++;
    int last = -1, len = 0, ans = 0, tot = 0, l = -1, r = -1;
    for (int i = 1; i <= n; i++) {
        if (s[i] == 'G') {
            if (l == -1) l = i;
            tot++; r = i;
            ans = max(ans, tot);
            if (last != -1) {
                if (last + 2 == l && len + tot + 1 <= c) {
                    ans = max(ans, len + tot + 1);
                }
            }
        } else {
            if (tot + 1 <= c) ans = max(ans, tot + 1);
            len = tot; 
            last = r;
            l = r = -1;
            tot = 0;
        }
    }
    if (tot + 1 <= c) ans = max(ans, tot + 1);
    cout << ans << endl;
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
#include <map>
#include <unordered_map>
#include <functional>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

int n, m, pre[maxn];
unordered_map< int,vector<int> > mp;

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1, s, r; i <= n; i++) {
        scanf("%d%d", &s, &r);
        mp[s].push_back(r);
    }
    for (auto& x: mp) sort(x.second.begin(), x.second.end(), greater<int>());
    int ans = 0;
    for (int len = 1; len <= n; len++) {
        int tot = 0;
        unordered_map< int,vector<int> > nmp;
        vector<int> v;
        for (auto& x: mp) {
            if ((int)x.second.size() < len) {
                v.push_back(x.first); continue;
            }
            pre[x.first] += x.second[len - 1];
            if (pre[x.first] > 0) tot += pre[x.first];
        }
        for (int& x: v) mp.erase(x);
        ans = max(ans, tot);
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
const int maxn = 100000 + 5;

int n, a[maxn];
vector<PII> ans;

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    vector<int> v, v2;
    for (int i = 1; i <= n; i++) if (a[i] == 1) v.push_back(i); else v2.push_back(i);
    int len = 0;
    for (int i = 1; i < (int)v2.size(); i++) {
        len++;
        ans.push_back({v2[i - 1], v2[i]});
        a[v2[i - 1]]--; a[v2[i]]--;
    }
    if (!v.empty()) {
        if (v2.empty()) return puts("NO"), 0;
        len++;
        ans.push_back({v.back(), v2.front()});
        v.pop_back(); a[v2.front()]--;
        if (a[v2.front()] < 0) return puts("NO"), 0;
    }
    if (!v.empty()) {
        if (v2.empty()) return puts("NO"), 0;
        len++;
        ans.push_back({v2.back(), v.back()});
        v.pop_back(); a[v2.back()]--;
        if (a[v2.back()] < 0) return puts("NO"), 0;
    }
    for (int& x: v) {
        int flag = 1;
        for (int& y: v2) {
            if (!a[y]) continue;
            ans.push_back({x, y}); a[y]--;
            flag = 0;
            break;
        }
        if (flag) return puts("NO"), 0;
    }
    if ((int)ans.size() < n - 1) return puts("NO"), 0;
    cout << "YES " << len << endl;
    cout << (int)ans.size() << endl;
    for (auto& x: ans) printf("%d %d\n", x.first, x.second);
    return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#include <map>
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
const int maxn = 500000 + 5;

int n, c, a[maxn], pre[maxn], suf[maxn];
map< int,vector<int> > mp;

int main() {
    scanf("%d%d", &n, &c);
    for (int i = 1; i <= n; i++) scanf("%d", a + i), mp[a[i]].push_back(i);
    for (int i = 1; i <= n; i++) pre[i] = pre[i - 1] + int(a[i] == c);
    int ans = pre[n];
    for (auto& x: mp) {
        if (x.first == c) continue;
        auto v = x.second;
        int mx = -1e9, i = 0;
        for (int& x: v) {
            mx = max(mx, pre[x] - i);
            ans = max(ans, pre[n] - pre[x] + i + 1 + mx);
            i++;
        }
    }
    cout << ans << endl;
    return 0;
}
```

## G

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#include <queue>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const ll inf = 1ll << 60;
const int maxn = 3000 + 5;

struct Edge {
    int from, to; ll cap, flow;
    Edge(int u, int v, ll c, ll f): from(u), to(v), cap(c), flow(f) {}
};

struct Dinic {
    int n, m, s, t;
    vector<Edge> edges;
    vector<int> G[maxn];
    bool vis[maxn];
    int dep[maxn], cur[maxn];
    void init(int n) {
        this->n = n;
        for (int i = 0; i <= n; i++) G[i].clear();
        edges.clear();
    }
    void adde(int from, int to, ll cap) {
        edges.emplace_back(from, to, cap, 0);
        edges.emplace_back(to, from, 0, 0);
        m = edges.size();
        G[from].push_back(m - 2);
        G[to].push_back(m - 1);
    }
    bool bfs() {
        ms(vis, 0); ms(dep, 0);
        queue<int> q; q.push(s);
        dep[s] = 0; vis[s] = 1;
        while (!q.empty()) {
            int x = q.front(); q.pop();
            for (int i = 0; i < G[x].size(); i++) {
                Edge& e = edges[G[x][i]];
                if (!vis[e.to] && e.cap > e.flow) {
                    vis[e.to] = 1;
                    dep[e.to] = dep[x] + 1;
                    q.push(e.to);
                }
            }
        }
        return vis[t];
    }
    ll dfs(int x, ll a) {
        if (x == t || a == 0) return a;
        ll flow = 0, f;
        for (int& i = cur[x]; i < G[x].size(); i++) { 
            //从上次考虑的弧
            Edge& e = edges[G[x][i]];
            if (dep[x] + 1 == dep[e.to] && (f = dfs(e.to, min(a, e.cap - e.flow))) > 0) {
                e.flow += f;
                edges[G[x][i] ^ 1].flow -= f;
                flow += f;
                a -= f;
                if (a == 0) break;
            }
        }
        return flow;
    }
    ll get(int s, int t) {
        this->s = s, this->t = t;
        ll flow = 0;
        while (bfs()) {
            ms(cur, 0);
            flow += dfs(s, inf);
        }
        return flow;
    }
} f;

int n, m;

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1, x; i <= n; i++) {
        scanf("%d", &x);
        f.adde(m + i, n + m + 2, x);
    }
    ll sum = 0;
    for (int i = 1, u, v, d; i <= m; i++) {
        scanf("%d%d%d", &u, &v, &d);
        sum += d;
        f.adde(n + m + 1, i, d);
        f.adde(i, u + m, inf);
        f.adde(i, v + m, inf);
    }
    cout << sum - f.get(n + m + 1, n + m + 2) << endl;
    return 0;
}
```
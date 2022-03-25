---
mathjax: true
title: Codeforces Round 541 题解
tags:
  - Codeforces
  - Solution
  - Graph
categories:
  - Codeforces
date: 2019-02-25 13:04:44
---

# A Sea Battle

# B Draw!

按照时间顺序给定一场比赛某些时刻的比分，求最多有多少时刻出现平局。

时间按照不降序给出，相邻两个时间点可能会出现重合，维护一下最后统计的平局时间，防止遗漏。

# C Birthday

# D Gourmet choice

给定一个 $n \times m$ 的表，表示第一块 $n$ 个物品和第二块 $m$ 个物品之间的权值关系，大于，小于或相等，要求恢复出每个物品的权值。

将权值相同的点缩点后跑拓扑排序即可。

一开始缩点的时候没写 `find(x)` 挂了一堆 T^T。

# F Asya And Kittens

有一个 $1$ 到 $n$ 的排列，有 $n-1$ 次操作，每次将两个数字所属的块合并，这两个块一定相邻，要求恢复出原排列。

并查集维护一下所属关系，链表维护每个块内有哪些点。

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

int w1, h1, w2, h2;

int main() {
    cin >> w1 >> h1 >> w2 >> h2;
    ll ans = (h1 + h2 + 2) * 2ll;
    ans += w1 + w2 + abs(w1 - w2);
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
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

int n;
set<int> st;

int main() {
    scanf("%d", &n);
    int la = 0, lb = 0;
    scanf("%d%d", &la, &lb);
    int ans = min(la, lb) + 1;
    st.insert(min(la, lb));
    for (int i = 1, a, b; i < n; i++) {
        scanf("%d%d", &a, &b);
        ans += max(0, min(a, b) - max(la, lb));
        if (a >= max(la, lb) && b >= max(la, lb) && !st.count(max(la, lb))) ans++;
        st.insert(min(a, b));
        la = a; lb = b;
    }
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

int n, a[maxn];
map<int,int> mp;
vector<int> ans;

int main() {
    scanf("%d", &n);
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    sort(a, a + n);
    ans.push_back(a[0]); ans.push_back(a[1]);
    for (int i = 2; i < n; i++) {
        int mn = 2e9 + 7, p = -1, sz = (int)ans.size();
        for (int j = 0; j < ans.size(); j++) {
            if (abs(a[i] - ans[(j + sz - 1) % sz]) + abs(a[i] - ans[j]) <= mn) {
                mn = abs(a[i] - ans[(j + sz - 1) % sz]) + abs(a[i] - ans[j]);
                p = j;
            }
        }
        vector<int> v;
        for (int i = 0; i < p; i++) v.push_back(ans[i]);
        v.push_back(a[i]);
        for (int i = p; i < sz; i++) v.push_back(ans[i]);
        ans = v;
    }
    for (int& x: ans) printf("%d ", x);
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
#include <assert.h>
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
const int maxn = 2000 + 5;

int n, m, pre[maxn], G[maxn][maxn], deg[maxn], ans[maxn];
char tmp[maxn];
vector<int> block[maxn], edge[maxn];

int find(int x) { return x == pre[x] ? x : pre[x] = find(pre[x]); }

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n + m; i++) pre[i] = i;
    for (int i = 1; i <= n; i++) {
        scanf("%s", tmp + 1);
        for (int j = 1; j <= m; j++) {
            if (tmp[j] == '<') G[i][j + n] = 1;
            else if (tmp[j] == '>') G[j + n][i] = 1;
            else {
                int x = find(i), y = find(j + n);
                pre[x] = y;
            }
        }
    }
    for (int i = 1; i <= n + m; i++) block[find(i)].push_back(i);
    int k = 0;
    for (int i = 1; i <= n + m; i++) {
        if (block[i].empty()) continue;
        k++; 
        // for (int& x: block[i]) bel[x] = k;
        for (int k = 0; k < block[i].size(); k++) {
            for (int j = k + 1; j < block[i].size(); j++) {
                int u = block[i][j], v = block[i][k];
                if (G[u][v] || G[v][u]) return puts("No"), 0;
            }
        }
    }
    dbg(k);
    for (int i = 1; i <= n + m; i++) for (int j = 1; j <= n + m; j++) {
        if (G[i][j]) {
            edge[find(i)].push_back(find(j));
            deg[find(j)]++;
        }
    } 
    vector<int> v;
    // for (int i = 1; i <= k; i++) if (deg[i] == 0) ans[i] = 1, v.push_back(i); 
    int cnt = 0;
    for (int i = 1; i <= n + m; i++) if (pre[i] == i && deg[i] == 0) ans[i] = 1, v.push_back(i);
    while (!v.empty()) {
        int tp = v.back(); v.pop_back();
        for (int& x: edge[tp]) {
            deg[x]--; 
            if (deg[x] == 0) ans[x] = ans[tp] + 1, v.push_back(x);
        }
        cnt++;
    }
    if (cnt < k) return puts("No"), 0;
    puts("Yes");
    for (int i = 1; i <= n + m; i++) printf("%d%c", ans[find(i)], " \n"[i == n]);
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
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 200000 + 5;

int n, pre[maxn], head[maxn], nxt[maxn];

int find(int x) { return x == pre[x] ? x : pre[x] = find(pre[x]); }

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) pre[i] = head[i] = i;
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        int x = find(u), y = find(v);
        pre[y] = x;
        nxt[head[x]] = y;
        head[x] = head[y];
    }
    for (int i = 1; i <= n; i++) if (pre[i] == i) {
        int tot = i;
        while (tot) {
            printf("%d ", tot); tot = nxt[tot];
        }
    }
    return 0;
}
```
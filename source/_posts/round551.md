---
mathjax: true
title:  Codeforces Round 551 题解
tags:
  - Codeforces
  - Solution
  - Tree
  - Interaction
categories:
  - Codeforces
date: 2019-04-14 01:50:24
---

真 口胡选手（赛后补交全挂了，做了 $3$ 个假题可太 zz 了

# A Serval and Bus

有 $n$ 个班车，始发时间 $s_i$，之后每隔 $t_i$ 发一班，求一个人在 $t$ 时候最早在何时上车。

求一下上车时间的最小值。

如果还未发车，在上车时间为始发时刻；否则，求下一辆车何时到达，上取整即可。

# B Serval and Toy Bricks

# C Serval and Parenthesis Sequence	

给定一个长度为 $n$ 的括号序列，其中某些位置不确定，求一个匹配的括号序列，使得其自身外每个前缀都不匹配。

显然尽量选左括号即可。

# D Serval and Rooted Tree

给定一棵 $n$ 个点的有根树，每个点上有一个标记 $\min$ 和 $\max$，有 $k$ 个叶子，每个叶子安排一个 $1$ 到 $k$ 的权值。

对于每个非叶子结点，他的权值是对其儿子做 $\min$ 或 $\max$ 操作，求根节点的最大权值。

对于每个结点维护其至少小于等于几个数。

一开始，对于叶子结点显然小于等于其自身。

考虑非叶子结点，如果是取 $\max$ 操作，则会从叶子里取一个小于等于个数最少的，如果是取 $\min$ 操作，由于子树之间不相交，需要把每个儿子小于等于个数加起来即可。

# E Serval and Snake

给了一个 $n \times n$ 的网格图，上面有一条蛇，你需要找到这个蛇的两端。

询问至多 $2019$ 次，每次询问一个矩形边框和蛇的相交次数，其中 $2\le n \le 1000$。

首先询问左边一整块，如果相交次数为奇数，那么两端一定分布在分界线两边，否则在同侧。

假设两端不在同一列，在询问情况一定是，某一个前缀一直是偶数，某一个后缀一直是偶数，只有中间是奇数，则这两个分界线上分别有 $1$ 个端点。

对于行，同样做这件事，询问次数至多为 $1998$ 次。

这样，我们会扣出 $4$ 条分界线，就有两组解，询问一下某个点，显然如果是头尾的话，则相交次数一定为 $1$，这样就找到了端点。

如果在同一行或者同一列，则有一个方向没有分界线。

最后这 $20$ 次询问，用来二分得到那个对应位置。

我们注意到同上类似的事情，如果一个矩形内仅有 $1$ 个端点，则相交次数一定为奇数，用这个条件二分即可。

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
using namespace std;
const int maxn = 100000 + 5;

int n, T, s[maxn], t[maxn];

int main() {
    scanf("%d%d", &n, &T);
    int mn = 1e9, p;
    for (int i = 1; i <= n; i++) {
        scanf("%d%d", s + i, t + i);
    }
    mn = 1e9;
    for (int i = 1; i <= n; i++) {
        if (s[i] > T) {
            if (s[i] < mn) mn = s[i], p = i;
            continue;
        }
        int x = (T - s[i]) / t[i];
        if ((T - s[i]) % t[i]) x++;
        int tot = x * t[i] + s[i];
        if (tot < mn) mn = tot, p = i;
    }
    cout << p;
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
const int maxn = 1000 + 5;

int n, m, h, a[maxn], b[maxn], c[maxn][maxn];

int main() {
    scanf("%d%d%d", &n, &m, &h);
    for (int i = 1; i <= m; i++) scanf("%d", b + i);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++) scanf("%d", &c[i][j]);
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            if (c[i][j] == 0) {
                printf("0 "); continue;
            }
            if (a[i] == b[j]) printf("%d ", a[i]);
            else printf("%d ", min(a[i], b[j]));
        }
        puts("");
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
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

int n; char s[maxn], ans[maxn];

int main() {
    scanf("%d%s", &n, s + 1);
    if (n % 2) return puts(":("), 0;
    int c = 0, l = 0;
    for (int i = 1; i <= n; i++) if (s[i] == '(') l++;
    for (int i = 1; i <= n; i++) {
        ans[i] = s[i];
        if (s[i] == '(') c++;
        else if (s[i] == ')') c--;
        else {
            if (l < n / 2) {
                ans[i] = '('; l++; c++;
            } else {
                ans[i] = ')'; c--;
            }
        }
        if (i < n && c <= 0) return puts(":("), 0;
    }
    if (c <= 0) cout << ans + 1 << endl;
    else puts(":(");
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
const int inf = 1 << 30;
const int maxn = 300000 + 5;

vector<int> edge[maxn];
int n, k, key[maxn], deg[maxn];

int l[maxn], r[maxn];
void dfs(int u) {
    for (int& v: edge[u]) {
        dfs(v);
        // r[u] += r[v]; l[u] += l[v];
        if (key[u]) {
            r[u] = min(r[u], r[v]);
            if (l[u] == inf) l[u] = 0;
            l[u] += l[v];
        } else {
            if (r[u] == inf) r[u] = 0;
            r[u] += r[v];
            l[u] = max(l[u], l[v]);
        }
    }
    dbg(u, r[u]);
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", key + i);
    for (int i = 2, f; i <= n; i++) {
        scanf("%d", &f); edge[f].push_back(i); deg[f]++;
    }
    for (int i = 1; i <= n; i++) if (deg[i] == 0) k++;
    for (int i = 1; i <= n; i++) if (deg[i] == 0) l[i] = 1, r[i] = 1; else r[i] = inf, l[i] = 0;
    dfs(1);
    cout << max(l[1], k - r[1] + 1);
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
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int query(int x1, int y1, int x2, int y2) {
    cout << "? " << x1 << " " << y1 << " " << x2 << " " << y2 << endl;
    int r = 0; cin >> r; return r;
}
void answer(int x1, int y1, int x2, int y2) {
    cout << "! " << x1 << " " << y1 << " " << x2 << " " << y2 << endl;
}

int n;

int main() {
    cin >> n;
    int l = -1, r = -1, d = -1, u = -1;
    for (int i = 1; i < n; i++) {
        int ans = query(1, 1, i, n);
        if (ans % 2) {
            if (u == -1) u = i;
            d = i + 1;
        }
    }
    for (int i = 1; i < n; i++) {
        int ans = query(1, 1, n, i);
        if (ans % 2) {
            if (l == -1) l = i;
            r = i + 1;
        }
    }
    dbg(l, r, d, u);
    int x1, y1, x2, y2;
    if (l == -1) {
        int x = 1, y = n, ans = 1;
        while (x <= y) {
            int m = (x + y) / 2;
            if (query(1, 1, u, m) % 2) y = m - 1, ans = m;
            else x = m + 1;
        }
        y1 = y2 = ans; x1 = u; x2 = d;
    } else if (d == -1) {
        int x = 1, y = n, ans = 1;
        while (x <= y) {
            int m = (x + y) / 2;
            if (query(1, 1, m, l) % 2) y = m - 1, ans = m;
            else x = m + 1;
        }
        x1 = x2 = ans; y1 = l; y2 = r;
    } else {
        if (query(d, l, d, l) == 1) {
            x1 = d; x2 = u; y1 = l; y2 = r;
        } else {
            x1 = d; x2 = u; y1 = r; y2 = l;
        }
    }
    answer(x1, y1, x2, y2);
    return 0;
}
```
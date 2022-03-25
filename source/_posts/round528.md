---
mathjax: true
title: Codeforces Round 528 题解
tags:
  - Codeforces
  - Solution
  - Tree
categories:
  - Codeforces
date: 2018-12-24 00:31:17
---

# A Right-Left Cipher

模拟。

# B Div Times Mod

求解关于 $x$ 的不定方程 $(x / k) * (x \% k) = n$ 的最小解。

对 $n​$ 质因数分解即可。

# C Connect Three

平面分成了无数 $1 \times 1$ 的方格，给定 3 个方格的坐标，用最少的方格让这三个联通。

枚举两两之间的 6 个最近点，找这个最近点与第三个点的最短路径。

# D Minimum Diameter Tree

给一棵树，给树上每条边加一个非负实数范围上的权值，权值总和为 $S$，最小化树的直径。

显然，对于任意一条直径，都会经过两个度数为 1 的顶点，那么必须要给每个这样的叶子上的边平均分配 $S$。

二分了一个小时是怎么回事啊？

<!--more-->

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <string>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

char s[maxn]; int n;

int main(){
    cin >> s; n = strlen(s);
    if (n == 1) return cout << s, 0;
    string ans;
    if (n % 2) ans.push_back(s[n / 2]);
    for (int i = 0; i < n / 2; i++) {
        if (n % 2) {
            ans.push_back(s[n / 2 + 1 + i]);
            ans.push_back(s[n / 2 - 1 - i]);
        } else {
            ans.push_back(s[n / 2 - i - 1]);
            ans.push_back(s[n / 2 + i]);
        }
    }
    cout << ans;
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

int n, k;

ll cal(int a, int b){
    if (b < 0 || b >= k) return 1e18;
    return 1ll * a * k + b;
}

int main(){
    cin >> n >> k;
    ll ans = 1e18;
    for (int i = 1; i * i <= n; i++) {
        if (n % i) continue;
        ans = min(ans, min(cal(i, n / i),cal(n / i, i)));
    }
    cout << ans;
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
const int maxn = 1000 + 5;

int x[3], y[3];
bool vis[maxn][maxn];

vector<PII> get(int x, int y, int xx, int yy) {
    vector<PII> v1, v2;
    for (int i = min(x, xx); i <= max(x, xx); i++) {
        if (!vis[i][y]) v1.push_back({i, y});
        if (!vis[i][yy]) v2.push_back({i, yy});
    }
    for (int i = min(y, yy); i <= max(y, yy); i++) {
        if (!vis[xx][i] && i != y) v1.push_back({xx, i});
        if (!vis[x][i] && i != yy) v2.push_back({x, i});
    }
    return (v1.size() < v2.size()) ? v1 : v2;
}

vector<PII> cal(int i, int j, int k) {
    int tx = x[i], ty = y[j];
    vector<PII> tmp; ms(vis, 0);
    for (int c = min(tx, x[j]); c <= max(tx, x[j]); c++) if (c != tx) tmp.push_back({c, ty}), vis[c][ty] = 1;
    for (int c = min(ty, y[i]); c <= max(ty, y[i]); c++) if (c != ty) tmp.push_back({tx, c}), vis[tx][c] = 1;

    // dbg(x[i], y[i]); dbg(x[j], y[j]); dbg(tx, ty);
    // for (auto& x: tmp) cout << x.first << ' ' << x.second << endl;
    // cout << "+\n";

    vector<PII> v = get(tx, ty, x[k], y[k]);
    // for (auto& x: v) cout << x.first << ' ' << x.second << endl;
    // cout << endl;
    for (auto& x: v) tmp.push_back(x);
    return tmp;
}

int main(){
    for (int i = 0; i < 3; i++) scanf("%d%d", x + i, y + i);
    if (x[0] == x[1] && x[1] == x[2]) {
        int l = min(y[0], min(y[1], y[2])), r = max(y[0], max(y[1], y[2]));
        printf("%d\n", r - l + 1);
        for (int i = l; i <= r; i++) printf("%d %d\n", x[0], i);
        return 0;
    }
    if (y[0] == y[1] && y[1] == y[2]) {
        int l = min(x[0], min(x[1], x[2])), r = max(x[0], max(x[1], x[2]));
        printf("%d\n", r - l + 1);
        for (int i = l; i <= r; i++) printf("%d %d\n", i, y[0]);
        return 0;
    }

    vector<PII> ans; int sz = 1e9;
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (i == j) continue;
            vector<PII> t = cal(i, j, 3 - i - j);
            if (t.size() < sz) ans = t, sz = t.size();
        }
    }

    printf("%d\n", sz);
    for (auto& x: ans) {
        printf("%d %d\n", x.first, x.second);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

int n, s, deg[maxn];
vector<int> edge[maxn];

int main(){
    scanf("%d%d", &n, &s);
    for (int i = 1, u, v; i < n; i++) scanf("%d%d", &u, &v), deg[u]++, deg[v]++;
    int c = 0; for (int i = 1; i <= n; i++) if (deg[i] == 1) c++;
    printf("%.8lf\n", 2.0 * double(s) / double(c));
    return 0;
}
```
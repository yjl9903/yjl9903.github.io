---
mathjax: true
title: Codeforces Round 514 题解
tags:
- Codeforces
- Solution
- Number Theory
- Binary Search
- Geometry
- Tree
categories:
- Codeforces
date: 2018-10-07 00:48:36
---

# A Cashier

给出 $n$ 个顾客的到达时间和服务他需要的时长，保证没有时间没有重叠，要求在总时间 $L$ 中能休息多少次，每次时长为 $a$。

A题就出了一个大锅，有点没睡醒？忘记在第一个顾客之前的时间也可以休息。

# B Forgery

判断给定的矩形图形是否可以用一个剪掉中心的 $3\times3$ 图形可重叠的覆盖。

直接对于每个可以进行覆盖的位置覆盖，最后判断是否覆盖完全即可。

# C Sequence Transformation

数论乱搞？

给定序列 $1,2,3 \dots n$ ，每次从中间删除一个，并向答案序列中添加原序列中剩余数字的 $\gcd$，要求字典序最大的答案序列。

首先，对于 $n\le3$，样例已经给出。如果 $n>3$，我们注意到由于 $\gcd(i,i+1)=1$，因此一开始会添加很多 $1$，那么要字典序最大显然 $1$ 的个数要最少，所以我们可以保留一个最小的因子，将不包含这个因子的数全部删除，可以发现我们选 $2$ 可以删除最少的数，这时剩下的数字是 $2,4,6 \dots$，且 $\gcd=2$，对于剩下的数字实际上可以除以 $2$，即转化为了原问题。因此，我们可以递归的重复这个过程，直到递归边界 $n\le3$，特判输出即可。

# D Nature Reserve

计算几何 + 二分 = 好题。

给定二维平面上的 $n$ 个点，要求一个与x轴相切的最小圆的半径，使得该圆能覆盖所有的点。

显然可以二分半径。

判断条件为是否能够构造出这样一个半径为 $r$ 圆，即是否存在 $a$，使得 $(x_i-a)^2+(y_i-r)^2 \le r^2$ 恒成立。化简得到，不等式对 $a$ 有解满足$\Delta=8ry_i-4y_i^2 \ge 0$ 且 $\max(x_i-\sqrt{2ry_i-y_i^2}) \le \min(x_i+\sqrt{2ry_i-y_i^2})$。

本题还需注意实数上二分的写法。

# E Split the Tree

树上二分好题。

给一棵以 $1$ 为根的有根树，现在要求你将树剖分成一些路径，满足每条路径上的点数小于等于 $L$，权值和小于等于 $S$，且路径上的点以此满足父子关系。

显然，每个叶子都必须分别划分进路径内，考虑一个贪心的做法，对任何一个点尽量在满足限制条件下往上爬，如果不尽量往上肯定不如当前的优。

对于每个点，在从根（$L$ 级祖先）到这个节点的路径的前缀和上二分，找到最浅的顶点满足这条路径权值和小于等于 $S$。

计算答案时，因为向上爬的路径是可以重合的，如果一个顶点的所有儿子的剖分路径都不能到达这个顶点，那么这个顶点必须作为一个新的剖分路径。否则，这个顶点在某个儿子的剖分路径内，贪心地取向上爬的最小深度顶点即可。

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
const int maxn = 100000 + 5;

int n, l, a, s[maxn], len[maxn];

int main(){
    ios::sync_with_stdio(false); cin.tie(0);
    cin >> n >> l >> a;
    int ans = 0, tot = 0;
    for (int i = 0; i < n; i++){
        cin >> s[i] >> len[i];
    }
    s[n] = l;
    while (tot + a <= s[0]) ans++, tot += a;
    for (int i = 0; i < n; i++){
        tot = s[i] + len[i];
        while (tot + a <= s[i + 1]) ans++, tot += a;
    }
    while (tot + a <= l) ans++, tot += a;
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

int n, m;
char map[maxn][maxn];
bool vis[maxn][maxn];

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 0; i < n; i++) scanf("%s", map[i]);
    for (int i = 0; i + 2 < n; i++){
        for (int j = 0; j + 2 < m; j++){
            if (map[i][j] == '.') continue;
            if (map[i][j + 1] == '.') continue;
            if (map[i][j + 2] == '.') continue;
            if (map[i + 1][j] == '.') continue;
            if (map[i + 1][j + 2] == '.') continue;
            if (map[i + 2][j] == '.') continue;
            if (map[i + 2][j + 1] == '.') continue;
            if (map[i + 2][j + 2] == '.') continue;
            for (int k = 0; k < 3; k++) vis[i][j + k] = vis[i + 2][j + k] = 1;
            vis[i + 1][j] = vis[i + 1][j + 2] = 1;
        }
    }
    for (int i = 0; i < n; i++) for (int j = 0; j < m; j++){
        if (map[i][j] == '.') continue;
        if (!vis[i][j]) {
            puts("NO"); return 0;
        }
    }
    puts("YES");
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
const int maxn = 1000000 + 5;

int gcd(int a, int b){return b ? gcd(b, a % b) : a;}

int n, a[maxn];

void dfs(int n, int base){
    if (n == 1){
        printf("%d", base);
    }
    if (n == 2){
        printf("%d %d", base, base * 2);
    }
    if (n == 3){
        printf("%d %d %d", base, base, base * 3);
    }
    if (n <= 3) return;
    for (int i = 1; i <= n; i += 2) printf("%d ", base);
    dfs(n / 2, base * 2);
}

int main(){
    scanf("%d", &n);
    if (n == 1) return puts("1"), 0;
    if (n == 2) return puts("1 2"), 0;
    if (n == 3) return puts("1 1 3"), 0;
    dfs(n, 1);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;
const double eps = 1e-8;

int n, a[maxn], b[maxn];

bool check(double x){
    double l = -1e14, r = 1e14;
    for (int i = 0; i < n; i++){
        double d = 2.0 * x * b[i] - 1.0 * b[i] * b[i];
        if (d < eps) return 0;
        l = max(l, 1.0 * a[i] - sqrt(d)); r = min(r, 1.0 * a[i] + sqrt(d)); 
    }
    return r - l > eps;
}

int main(){
    scanf("%d", &n); 
    int flag = 0, cnt = 0;
    for (int i = 0; i < n; i++) {
        scanf("%d%d", a + i, b + i);
        if (!flag){
            if (b[i] > 0) flag = 1;
            else if (b[i] < 0) flag = -1;
            else {
                if (cnt) return puts("-1"), 0;
                else cnt = 1;
            }
        }
        else {
            if (b[i] * flag < 0) return puts("-1"), 0;
        }
        b[i] = abs(b[i]);
    }
    double l = -1e14, r = 1e14;
    for (int i = 0; i < 100; i++){
        double m = (l + r) / 2;
        if (check(m)) r = m;
        else l = m;
    }
    printf("%.8lf", l);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 998244353;
const int maxn = 100000 + 5;

vector<int> edge[maxn];
int n, l, w[maxn], ans, dep[maxn], tot, top[maxn], id[maxn], path[maxn];
ll s, pre[maxn];

int get(int u, int d) {
    int l = max(1, d - ::l + 1), r = d, ans = u;
    while (l <= r) {
        int m = (l + r) >> 1;
        if (pre[d] - pre[m - 1] <= s) r = m - 1, ans = id[m];
        else l = m + 1;
    }
    return ans;
}
void dfs(int u, int f) {
    dep[u] = dep[f] + 1;
    tot++; pre[tot] = pre[tot - 1] + w[u]; id[tot] = u;
    top[u] = get(u, tot);
    for (int& v: edge[u]) {
        dfs(v, u);
    }
    tot--;
}
int dfs(int u) {
    int r = -1;
    for (int& v: edge[u]) {
        int t = dfs(v);
        if (t == v) continue;
        if (r == -1 || dep[t] < dep[r]) r = t;
    }
    if (r != -1) return r;
    ans++;
    return top[u];
}

int main() {
    scanf("%d%d%I64d", &n, &l, &s); 
    for (int i = 1; i <= n; i++) {
        scanf("%d", w + i);
        if (w[i] > s) return puts("-1"), 0;
    }
    for (int i = 2, x; i <= n; i++) scanf("%d", &x), edge[x].push_back(i);
    dfs(1, 0); dfs(1);
    cout << ans << endl;
    return 0;
}
```


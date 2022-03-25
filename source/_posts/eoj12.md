---
mathjax: true
title: EOJ Monthly 2018.12 题解
date: 2019-01-12 00:20:37
tags:
- EOJ Monthly
- Solution
- Probability
- Tree
- Binary Search
categories:
- 题解
---

咕咕咕了一个月的补题。

# A 仰望星空

总共 $n$ 天，已知第一天权值为 $b$，最后一天权值为 $a$，每天权值不会增加，问一共有多少种可能的权值总和。

中间 $n-2$ 天的权值和的范围是 $[ (n-2) \times a, (n-2) \times b ]$，一共有 $(n-2)(b-a)+1$ 种。

# B 清点星辰

$n$ 个点随机均匀地撒在 $1 \times 1$ 的矩形内，求最短距离的期望。

随机模拟，注意保证总的复杂度为 $1e8$。

# C 她的名字

给一个长度为 $2000$ 的数字串，每次询问从母串中取出 $N$ 个字符，组成结尾为 $XY$ 的子序列方案数。

预处理每种数字的出现次数的后缀和。

预处理每一种 $XY$ 的情况，即每一个数字为 $X$ 的位置的后缀有多少 $Y$，前面有多少个数字。

对于每个询问，每个 $X$ 的位置 $pos$，对应后缀 $Y$ 的个数 $cnt$，答案为 $\sum {pos-1 \choose N-2 } \times cnt$。

注意保存一下重复的询问答案。

# E 管理孩子

二分答案。

对于每个子树，维护一个权值：这棵子树中的某个节点最多还能够覆盖多少节点（+）或者这棵子树还有多少个顶点没有被覆盖到（-），可以用正负号表示这两种权值（不会同时出现）。

dfs 到某一个节点时，考虑这个节点所有儿子的权值，这些权值有正有负，所有负的权值的和，也就是这个子树总共还有多个点没有被覆盖。

显然最多只有一个带有正权值的儿子对这个子树的覆盖有贡献，因为如果覆盖到了根节点，那么其他正儿子就不能再覆盖根节点了（不联通），所以需要选择一个权值最大的正儿子。

如果正儿子权值比较大，就让这个顶点覆盖这个子树所有未覆盖顶点，然后将还能够覆盖的个数回溯到当前子树的父亲。

如果正儿子权值比较小，那么就需要当前子树的父亲来覆盖自己，将还需覆盖的个数回溯回去。

如果构造成功，那么根节点会返回一个非负值。否则，表示根节点的父亲需要提供一些覆盖给这棵树，即构造失败。

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
const int maxn = 1000 + 5;

int n, a, b;

int main(){
    cin >> n >> a >> b;
    cout << 1ll * (n - 2) * (b - a) + 1 << endl;
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
#include <cstdlib>
#include <ctime>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

int n;
double tx[maxn], ty[maxn];

double dis(double x, double y){return sqrt(x * x + y * y);}
double cal(int n) {
    double sum = 0;
    int T = 1e8 / n / n;
    for (int t = 0; t < T; t++){
        for (int i = 0; i < n; i++){
            double x = double(rand()) / double(RAND_MAX), y = double(rand()) / double(RAND_MAX);
            // tx.push_back(x); ty.push_back(y);
            tx[i] = x; ty[i] = y;
        }
        double mn = 2;
        for (int i = 0; i < n; i++){
            for (int j = i + 1; j < n; j++){
                mn = min(mn, dis(tx[i] - tx[j], ty[i] - ty[j]));
            }
        }
        sum += mn;
    }
    return sum / double(T);
}

int main(){
    srand(233333);
    cin >> n;
    if (n >= 800) return printf("0.0000000"), 0;
    printf("%.5lf", cal(n));
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
const int maxn = 4000 + 5;
const ll mod = 1e9 + 7;

int gi(){
    char ch = getchar(); int r = 0;
    while (ch < '0' || ch > '9') ch = getchar();
    while (ch >= '0' && ch <= '9') r = r * 10 + ch - '0', ch = getchar();
    return r;
}

int f[maxn], inv[maxn], finv[maxn];
void init(){
    inv[1] = 1;
    for (int i = 2; i < maxn; i++) inv[i] = (mod - mod / i) * 1ll * inv[mod % i] % mod;
    f[0] = finv[0] = 1; 
    for (int i = 1; i < maxn; i++) {
        f[i] = f[i - 1] * 1ll * i % mod;
        finv[i] = finv[i - 1] * 1ll * inv[i] % mod;
    }
}
int C(int n, int m){
    if (m < 0 || m > n) return 0;
    return f[n] * 1ll * finv[n - m] % mod * finv[m] % mod;
}

int n, q, cnt[maxn][10]; char s[maxn];
// vector<int> pos[100], ans[100];
int pos[100][maxn], ans[100][maxn], len[100]; 
ll rec[maxn][100];

int main(){
    init(); ms(rec, -1);
    // scanf("%s%d", s + 1, &q); n = strlen(s + 1);
    scanf("%s", s + 1); n = strlen(s + 1); q = gi();

    for (int i = n; i >= 1; i--){
        cnt[i][s[i] - '0']++;
        for (int j = 0; j < 10; j++) cnt[i][j] += cnt[i + 1][j];
    }
    for (int i = 0; i <= 99; i++){
        int x = i / 10, y = i % 10, l = 0;
        for (int j = 1; j <= n; j++){
            if (s[j] - '0' == x && cnt[j + 1][y]){
                // pos[i].push_back(j - 1);
                // ans[i].push_back(cnt[j + 1][y]);
                pos[i][l] = j - 1;
                ans[i][l] = cnt[j + 1][y];
                l++;
            }
            len[i] = l;
        }
    }

    while (q--){
        int m, xy; 
        // scanf("%d%d", &m, &xy);
        m = gi(); xy = gi();
        if (m > n || m == 1) {
            puts("0"); continue;
        }
        if (rec[m][xy] != -1) {
            printf("%lld\n", rec[m][xy]);
            continue;
        }
        ll res = 0;
        // for (int i = 0; i < pos[xy].size(); i++){
        //     res += 1ll * C(pos[xy][i], m - 2) * ans[xy][i] % mod;
        //     res %= mod;
        // }
        for (int i = 0; i < len[xy]; i++) {
            res += 1ll * C(pos[xy][i], m - 2) * ans[xy][i] % mod;
            if (res >= mod) res -= mod;
        }
        rec[m][xy] = res;
        printf("%lld\n", res);
    }
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
const int mod = 1e9 + 7;
const int maxn = 200000 + 5;

vector<int> edge[maxn];
int n, k, tag, key[maxn];

int dfs(int u, int f) {
    int sum = 1, mx = 0;
    if (key[u]) mx = tag;
    for (int& v: edge[u]) {
        if (v == f) continue;
        int x = dfs(v, u);
        if (x < 0) {
            sum += -x;
        } else if (x > 0) {
            mx = max(mx, x);
        }
    }
    if (mx >= sum) return mx - sum;
    return -sum;
}
int check(int x) {
    tag = x;
    return dfs(1, 0) >= 0;
}

int main(){
    scanf("%d%d", &n, &k);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    for (int i = 1, x; i <= k; i++) {
        scanf("%d", &x); key[x] = 1;
    }
    int l = 0, r = maxn, ans = 0;
    while (l <= r) {
        int m = l + r >> 1;
        if (check(m)) r = m - 1, ans = m;
        else l = m + 1;
    }
    printf("%d", ans);
    return 0;
}
```
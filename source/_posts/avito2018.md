---
mathjax: true
title: Avito Cool Challenge 2018 题解
tags:
- AtCoder
- Solution
- Graph
- Number Theory
categories:
- AtCoder
date: 2018-12-17 11:33:50
---

# A Definite Game

给一个数字 $n \le 1e9$  ，每次操作可以将 $n$ 减去一个不是其因子的数，求最小的结果。

显然，$gcd(n, n-1)=1$，直接输出 $1$ 即可。

注意特判掉 $n=2$。

# B Farewell Party

$n$ 个人都戴了一顶某个颜色的帽子，第 $i$ 人的帽子颜色和 $a_i$ 个人不一样，要求恢复出每个人的帽子颜色。

$a_i$ 可以转化为第 $i$ 个人的帽子颜色一共出现 $n-a_i$ 次，对于这种颜色一共有 $n-a_i$ 倍数数量的人与之颜色相同。

有点难写T^T？

# C Colorful Bricks

一行有 $n$ 个位置，用 $m$ 种颜色染色，要求恰好有 $k$ 个位置，满足该位置和其左边位置颜色不同，求方案数。

显然，$dp[i][j]$ 表示前 $i$ 个位置恰好有 $j$ 个位置满足条件：
$$
dp[i][j] = dp[i-1][j]+(m-1)dp[i-1][j-1]
$$

# D Maximum Distance

给一个无向带权有重边自环的连通图，给出 $k$ 个关键点，求每个关键点到最远的关键点的距离（距离是路径最大边的长度的最小值）。

考虑对边权排序，按照 $Kruskal$ 的方式加边，维护每个联通块内关键点的个数，当所有关键点加入到一个联通块时，这里的边权就是相同的 $k$ 个答案。

证明，每次合并两个联通块时，联通块内的路径距离一定小于当前的边，当前边加入后，两个联通块内的答案就是一个块内关键点到另外一个块内的关键点的路径，路径距离即为新加入的边。又由于如果一个联通块内没有关键点，实际上答案并不会更新，实际上由于图的联通性，当关键点恰好加入一个块内时，所有点的答案都被更新了，并且向这个联通块内加入点并不会影响答案。

# E Missing Numbers

给一个长度为偶数的序列的所有偶数项 $a[2i]$，满足所有前缀和都是完全平方数。

简单推一推可知前缀和 $s[2i-1]$，$s[2i]$ 仅与给出的 $a[2i]$ 有关，即要求解方程 $p^2-q^2=a[2i]$，即 $(p-q)(p+q)=a[2i]$，质因数分解即可。另外由于前缀和要求严格单调，每次贪心地解出最小的 $s[2i]$ 。

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



int main(){
    int n; cin >> n;
    if (n == 2) puts("2");
    else puts("1");
    return 0;
}
```

## B

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

int n, a[maxn], cnt[maxn], ans[maxn], used[maxn], col[maxn];

int main(){
    scanf("%d", &n); 
    for (int i = 1; i <= n; i++) {
        scanf("%d", a + i), a[i] = n - a[i];
        cnt[a[i]]++;
    }
    for (int i = 1; i <= n; i++) {
        if (cnt[i] == 0) continue;
        if (cnt[i] % i) return puts("Impossible"), 0;

    }
    puts("Possible");
    set<int> s; for (int i = 1; i <= n; i++) s.insert(i);
    for (int i = 1; i <= n; i++) {
        int x = a[i];
        if (col[x]) {
            ans[i] = col[x];
        } else {
            ans[i] = col[x] = *s.begin();
            s.erase(s.begin());
        }
        used[x]++;
        // cout << used[x] << endl;
        if (used[x] % x == 0) {
            if (used[x] == cnt[x]) continue;
            col[x] = *s.begin();
            s.erase(s.begin());
        }
    }
    for (int i = 1; i <= n; i++) {
        printf("%d ", ans[i]);
    }
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 2000 + 5;
const ll mod = 998244353;

int n, m, k;
ll dp[maxn][maxn];

int main(){
    cin >> n >> m >> k;
    dp[1][0] = m;
    for (int i = 2; i <= n; i++) {
        dp[i][0] = dp[i - 1][0];
        for (int j = 1; j < i; j++) {
            dp[i][j] = (dp[i - 1][j] + (m - 1) * dp[i - 1][j - 1] % mod) % mod;
        }
    }
    cout << dp[n][k] % mod << endl;
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
#include <utility>
#include <map>
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
const int maxn = 100000 + 5;

struct node{
    int u, v, w;
    bool operator<(const node& b)const{
        return w < b.w;
    }
};
vector<node> q;

int n, m, k, key[maxn], pre[maxn], cnt[maxn], sp[maxn], ans[maxn];

int find(int x){return pre[x] == x ? x : pre[x] = find(pre[x]);}

int main(){
    scanf("%d%d%d", &n, &m, &k);
    for (int i = 1; i <= n; i++) pre[i] = i;
    for (int i = 1; i <= k; i++) scanf("%d", key + i), cnt[key[i]] = 1;
    for (int i = 1, u, v, w; i <= m; i++) {
        scanf("%d%d%d", &u, &v, &w);
        q.push_back({u, v, w});
    }
    sort(q.begin(), q.end());
    int res = 0;
    for (node& x: q) {
        int b1 = find(x.u), b2 = find(x.v);
        if (b1 == b2) continue;

        cnt[b2] += cnt[b1];
        pre[b1] = b2;
        if (cnt[b2] == k) {
            res = x.w; break;
        }
    }
    for (int i = 1; i <= k; i++) {
        printf("%d ", res);
    }
    return 0;
}
```

# E

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

int n;
vector<int> fac[maxn * 2];
ll ans[maxn];

int main(){
    for (int i = 1; i < maxn * 2; i++) for (int j = i; j < maxn * 2; j += i)
        fac[j].push_back(i);
    scanf("%d", &n); 
    ll last = 0;
    for (int i = 2, x; i <= n; i += 2) {
        scanf("%d", &x);
        ll ansa = 1e18, ansb = 1e18;
        for (int d: fac[x]) {
            // a + b = x / d, b - a = d (a < b)
            ll a = x / d - d, b = x / d + d;
            if (a < 1 || b < 1 || a % 2 || b % 2 || a >= b) continue;
            a /= 2; b /= 2;
            if (a * a > last && b < ansb) {
                ansa = a; ansb = b;
            } 
        }
        if (ansa > 1e13) return puts("No"), 0;
        ans[i - 1] = ansa * ansa;
        ans[i] = last = ansb * ansb;
    }
    puts("Yes");
    for (int i = 1; i <= n; i++) printf("%lld ", ans[i] - ans[i - 1]);
    return 0;
}
```


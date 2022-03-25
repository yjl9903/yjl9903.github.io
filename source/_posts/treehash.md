---
mathjax: true
title: 树哈希
date: 2019-08-13 20:20:57
tags:
- Tree
- String
categories:
- 树
---

$$
f_{now}=1+\sum f_{son_{now,i}} \times prime(size_{son_{now,i}})
$$

其中 $f_x$ 为以结点 $x$ 为根的子树对应的哈希值。

$size_{x}$ 表示以结点 $x$ 为根的子树大小。

$son_{x,i}$ 表示 $x$ 所有子结点之一（不用排序）。

$prime(i)$ 表示第 $i$ 个质数。

> 参考博客 [树hash](https://www.cnblogs.com/huyufeifei/p/10817673.html)

<!--more-->

# HDu6647 Bracket Sequences on Tree

题目要求的是遍历一棵无根树产生的本质不同括号序列方案数。

首先，注意到一个结论，对于两棵有根树，如果他们不同构，一定不会生成相同的括号序列。我们先考虑遍历有根树能够产生的本质不同括号序列方案数，假设我们当前考虑的子树根结点为 $u$，记 $f(u)$ 表示这棵子树的方案数，$son(u)$ 表示 $u$ 的儿子结点集合，从 $u$ 开始往下遍历，顺序可以随意选择，产生 $|son(u)|!$ 种排列，遍历每个儿子结点 $v$，$v$ 的子树内有 $f(v)$ 种方案，因此有 $f(u)=|son(u)|! \cdot \prod_{v\in son(u)} f(v)$。但是，同构的子树之间会产生重复，$f(u)$ 需要除掉每种本质不同子树出现次数阶乘的乘积，类似于多重集合的排列。

通过上述树形 dp，可以求出根结点的方案数，再通过 up and down 树形 dp，将父亲结点的哈希值和方案信息转移给儿子，可以求出以每个结点为根时的哈希值和方案数，每种不同的子树只需要计数一次即可。

注意，本题数据较强，树哈希很容易发生冲突。这里提供一个比较简单的解决方法，求出一个结点子树的哈希值后，可以将其前后分别插入一个值再计算一遍哈希值。

## 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#include <set>
#include <map>
#ifdef XLor
  #define dbg(args...) do { cout << #args << " -> "; err(args); } while (0)
  void err() { std::cout << std::endl; }
  template<typename T, typename...Args>
  void err(T a, Args...args) { std::cout << a << ' '; err(args...); }
#else
  #define dbg(...)
#endif
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef unsigned long long ull;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

namespace sieve{
    const int maxp = 2000000 + 5;
    int vis[maxp], prime[maxp], tot;
    void init() {
        ms(vis, 0);
        for (int i = 2; i < maxp; i++) {
            if (!vis[i]) prime[++tot] = i;
            for (int j = 1; j <= tot && prime[j] * i < maxp; j++) {
                vis[i * prime[j]] = 1;
                if (i % prime[j] == 0) break;
            }
        }
    }
}
namespace MyIO {
    struct fastIO {
        char s[100000]; int it,len;
        fastIO() { it = len = 0; }
        inline char get() {
            if (it < len) return s[it++];
            it = 0; len = fread(s, 1, 100000, stdin);
            if (len == 0) return EOF;
            else return s[it++];
        }
        bool notend() {
            char c = get();
            while(c == ' ' || c == '\n') c = get();
            if (it > 0) it--;
            return c != EOF;
        }
    } buff;
    inline int gi() {
        int r = 0; bool ng = 0; 
        char c = buff.get();
        while (c != '-' && (c < '0' || c > '9')) c = buff.get();
        if (c == '-') ng = 1, c = buff.get();
        while (c >= '0' && c <= '9') r = r * 10 + c - '0', c = buff.get();
        return ng ? -r : r;
    }
}
namespace {
    inline int add(int x, int y) {
        x += y;
        return x >= mod ? x -= mod : x;
    }
    inline int sub(int x, int y) {
        x -= y;
        return x < 0 ? x += mod : x;
    }
    inline int mul(int x, int y) {
        return 1ll * x * y % mod;
    }
    inline int qpow(int x, ll n) {
        int r = 1;
        while (n > 0) {
            if (n & 1) r = 1ll * r * x % mod;
            n >>= 1; x = 1ll * x * x % mod;
        }
        return r;
    }
    inline int inv(int x) {
        return qpow(x, mod - 2);
    }
}
using sieve::prime;
using MyIO::gi;

int ping[maxn], pingv[maxn];

int n, ans, siz[maxn];
vector<int> edge[maxn];
map<ull,int> uqc[maxn];
map<ull,int>::iterator it;

ull hashval[maxn], hashrt[maxn];
ull srchashval[maxn], srchashrt[maxn];
int dp[maxn], rdp[maxn];
ull pack(ull val, int sz) {
    return 2ull + 3ull * val + 7ull * prime[sz + 1];
}
void predfs(int u, int ff) {
    siz[u] = dp[u] = 1;
    hashval[u] = 1;
    int sz = 0;
    for (int v: edge[u]) {
        if (v == ff) continue;
        predfs(v, u);
        sz++;
        siz[u] += siz[v];
        dp[u] = mul(dp[u], dp[v]);
        uqc[u][hashval[v]]++;
        hashval[u] += hashval[v] * prime[siz[v]];
    }

    srchashval[u] = hashval[u];
    hashval[u] = pack(hashval[u], siz[u]);

    dp[u] = mul(dp[u], ping[sz]);
    for (it = uqc[u].begin(); it != uqc[u].end(); it++) {
        dp[u] = mul(dp[u], pingv[it->second]);
    }
}
set<ull> qc;
void dfs(int u, int ff) {
    if (!qc.count(hashrt[u])) {
        qc.insert(hashrt[u]);
        ans = add(ans, rdp[u]);
    }
    for (int v: edge[u]) {
        if (v == ff) continue;

        ull tmp = srchashrt[u] - hashval[v] * prime[siz[v]];
        tmp = pack(tmp, n - siz[v]);

        uqc[v][tmp]++;
        srchashrt[v] = srchashval[v] + tmp * prime[n - siz[v]];
        hashrt[v] = pack(srchashrt[v], n);

        int tdp = mul(rdp[u], inv(dp[v]));
        tdp = mul(tdp, inv((int)edge[u].size()));
        tdp = mul(tdp, uqc[u][hashval[v]]);
        rdp[v] = mul(dp[v], tdp);
        rdp[v] = mul(rdp[v], (int)edge[v].size());
        rdp[v] = mul(rdp[v], inv(uqc[v][tmp]));

        dfs(v, u);
    }
}

int main() {
    sieve::init(); ping[0] = pingv[0] = 1;
    for (int i = 1; i < maxn; i++) {
        ping[i] = mul(ping[i - 1], i);
        pingv[i] = mul(pingv[i - 1], inv(i));
    }
    int T = gi();
    while (T--) {
        n = gi();
        for (int i = 2, u, v; i <= n; i++) {
            u = gi(); v = gi();
            edge[u].push_back(v);
            edge[v].push_back(u);
        }

        predfs(1, 0);
        ans = 0; qc.clear();
        rdp[1] = dp[1]; 
        hashrt[1] = hashval[1];
        srchashrt[1] = srchashval[1];
        dfs(1, 0);

        printf("%d\n", ans);

        for (int i = 1; i <= n; i++) {
            edge[i].clear();
            uqc[i].clear();
        }
    }
    return 0;
}
```
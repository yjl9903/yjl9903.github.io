---
mathjax: true
title: Educational Round 65 题解
tags:
  - Codeforces
  - Solution
  - Fenwick Tree
categories:
  - Codeforces
  - Educational Round
date: 2019-05-22 19:41:12
---

# A Telephone Number

# B Lost Numbers

# C News Distribution

# D Bicolored RBS

给你一个括号匹配好的括号序列，现在你要将其分成两个，使得长度最大的那个最小。

想都没想直接写了二分，然后就过了。

实际上，直接构造奇偶就行了。

# E Range Deleting

（惨遭带走

给定一个序列 $a$，每个数字范围在 $1$ 到 $x$ 之间，定义 $f(l,r)$ 表示把数值大小在 $[l,r]$ 到内的全部剔除，检查剩余数字是否单调不减，对 $f$ 求和。

显然被剔除之后剩下一个前缀和后缀，对每一个前缀找到一个最长的后缀使得能拼成满足要求的序列。

因此，只需要预处理每一个前缀是否满足条件即可，记录一下每种数字的第一个和最后一个出现位置。

# F Scalar Queries

给定一个序列 $a$，定义 $f(l,r)$ 表示把 $a[l\dots r]$ 抠出来排序，生成 $b$ 序列，$f(l,r)=\sum_{i=1}^{r-l+1} i \cdot b_i$，对 $f(l,r)$ 求和。

考虑每个位置对答案的总贡献，也就是对应位置的权值乘上系数。我们首先注意到，一个点系数之和前后有多少数字比它小有关，因此考虑从小到大枚举。

记当前枚举的位置为 $pos$，记 $g(l,r)$ 表示 $[l,r]$ 内有多少数字小于等于 $a_{pos}$。

因此，贡献就等于 $\sum_{i=1}^{pos} \sum_{j=pos}^{n} g(i, pos) + g(pos, j) - 1$，其中 $pos$ 被算了两次。

发现这两个维度独立，上式也就是 $\sum_{i=1}^{pos} g(i,pos) \times \sum_{j=pos}^n g(pos,j) - pos \cdot (n-pos+1)$。

最后，先考虑前一部分，发现如果之前已经算过了 $i,i < pos $，他对答案的贡献也就是 $i$，后一部分也是类似。

单点修改查询前缀和，树状数组维护即可。

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

int n; char s[maxn];

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%s", &n, s + 1);
        int pos = -1;
        for (int i = 1; i <= n; i++) if (s[i] == '8') {
            pos = i; break;
        }
        if (pos == -1) puts("NO");
        else {
            if (n - pos >= 10) puts("YES");
            else puts("NO");
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
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int g[6] = { 4, 8, 15, 16, 23, 42 };

PII query(int i, int j) {
    cout << "? " << i << " " << j << endl;
    int x; cin >> x;
    for (int i = 0; i < 6; i++) {
        for (int j = i + 1; j < 6; j++) {
            if (g[i] * g[j] == x) {
                // cout << g[i] << ' ' << g[j] << endl;
                return { g[i], g[j] };
            }
        }
    }
}

int main() {
    vector<int> ans;
    PII x1 = query(1, 2);
    PII x2 = query(2, 3);
    if (x1.first == x2.first || x1.first == x2.second) {
        ans.push_back(x1.second);
        ans.push_back(x1.first);
        if (x1.first == x2.first) ans.push_back(x2.second);
        else ans.push_back(x2.first);
    } else {
        ans.push_back(x1.first);
        ans.push_back(x1.second);
        if (x1.second == x2.first) ans.push_back(x2.second);
        else ans.push_back(x2.first);
    }
    PII x3 = query(3, 4);
    if (x3.first == ans.back()) ans.push_back(x3.second);
    else ans.push_back(x3.first);
    PII x4 = query(4, 5);
    if (x4.first == ans.back()) ans.push_back(x4.second);
    else ans.push_back(x4.first);
    for (int i = 0; i < 6; i++) {
        int f = 0;
        for (int j = 0; j < 5; j++) if (ans[j] == g[i]) f = 1;
        if (f == 0) {
            ans.push_back(g[i]);
        }
    }
    cout << "!";
    for (int& x: ans) cout << " " << x;
    cout << endl;
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
const int maxn = 500000 + 5;

int n, m;
int pre[maxn], siz[maxn];
int find(int x) {
    return x == pre[x] ? x : pre[x] = find(pre[x]);
}
void join(int x, int y) {
    x = find(x); y = find(y);
    if (x == y) return ;
    pre[x] = y; siz[y] += siz[x];
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) pre[i] = i, siz[i] = 1;
    for (int i = 1, k, x; i <= m; i++) {
        scanf("%d", &k);
        if (k == 0) continue;
        scanf("%d", &x); k--;
        while (k--) {
            int y; scanf("%d", &y);
            join(x, y);
        }
    }
    for (int i = 1; i <= n; i++) cout << siz[find(i)] << ' ';
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
#include <cassert>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 200000 + 5;

int n; char s[maxn];

string t;
int check(int maxd) {
    t = "";
    int tot = 0, tot2 = 0, mx = 0; string a, b;
    for (int i = 1; i <= n; i++) {
        if (s[i] == '(') {
            if (tot < maxd) {
                tot++; 
                a += '(';
                t += '0';
            } else {
                tot2++; 
                b += '(';
                mx = max(tot2, mx);
                t += '1';
            }
        } else {
            if (tot > 0) {
                tot--; a += ')';
                t += '0';
            } else {
                assert(tot2 >= 0);
                tot2--; 
                b += ')';
                t += '1';
            }
        }
    }
    // cout << a << endl << b << endl;
    return mx <= maxd;
}

int main() {
    scanf("%d%s", &n, s + 1);
    check(2);
    int l = 1, r = n, ans = 0;
    string res;
    while (l <= r) {
        int m = (l + r) / 2;
        if (check(m)) res = t, r = m - 1;
        else l = m + 1;
    }
    // cout << ans;
    cout << res;
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
const int maxn = 1000000 + 5;

int n, x, a[maxn], pre[maxn], suf[maxn];
int first[maxn], last[maxn];

int main() {
    scanf("%d%d", &n, &x);
    for (int i = 1; i <= n; i++) {
        scanf("%d", a + i);
        if (first[a[i]] == 0) first[a[i]] = i;
        last[a[i]] = i;
    }
    suf[x + 1] = n + 1;
    for (int i = x; i >= 1; i--) {
        if (first[i] == 0) suf[i] = suf[i + 1];
        else {
            if (last[i] < suf[i + 1]) suf[i] = first[i];
            else suf[i] = 0;
        }
    }
    pre[0] = 0;
    for (int i = 1; i <= x; i++) {
        if (first[i] == 0) pre[i] = pre[i - 1];
        else {
            if (first[i] > pre[i - 1]) pre[i] = last[i];
            else pre[i] = n + 1;
        }
    }
    for (int i = 0; i <= x + 1; i++) dbg(i, pre[i]);
    for (int i = 0; i <= x + 1; i++) dbg(i, suf[i]);
    ll ans = 0;
    for (int i = 1; i <= x + 1; i++) {
        int id = lower_bound(pre, pre + i - 1, suf[i]) - pre;
        dbg(i, suf[i], id, pre[id]);
        ans += id;
    }
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
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 1e9 + 7;
const int inf = 1 << 30;
const int maxn = 500000 + 5;

inline void add(int& x, int y) {
    x += y; if (x >= mod) x -= mod;
}
inline void sub(int& x, int y) {
    x -= y; if (x < 0) x += mod;
}

int n, a[maxn], pos[maxn];

inline int lowbit(int x) {
    return x & -x;
}
struct BIT {
    int a[maxn];
    inline void update(int i, int x) {
        for (; i <= n; i += lowbit(i)) add(a[i], x);
    }
    inline int query(int i) {
        int r = 0;
        for (; i > 0; i -= lowbit(i)) add(r, a[i]);
        return r;
    }
} f1, f2;

int main() {
    scanf("%d", &n);
    vector<int> lsh;
    for (int i = 1; i <= n; i++) {
        scanf("%d", a + i);
        lsh.push_back(a[i]);
    }
    sort(lsh.begin(), lsh.end());
    for (int i = 1; i <= n; i++) {
        int rk = lower_bound(lsh.begin(), lsh.end(), a[i]) - lsh.begin() + 1;
        pos[rk] = i;
    }
    int ans = 0;
    for (int i = 1; i <= n; i++) {
        f1.update(pos[i], pos[i]);
        f2.update(pos[i], n - pos[i] + 1);
        int x = 1ll * (n - pos[i] + 1) * f1.query(pos[i]) % mod;
        int y = 1ll * pos[i] * ((f2.query(n) - f2.query(pos[i] - 1) + mod) % mod) % mod;
        int z = 1ll * pos[i] * (n - pos[i] + 1) % mod;
        add(ans, 1ll * x * lsh[i - 1] % mod); 
        add(ans, 1ll * y * lsh[i - 1] % mod); 
        sub(ans, 1ll * z * lsh[i - 1] % mod);
    }
    cout << ans << endl;
    return 0;
}
```
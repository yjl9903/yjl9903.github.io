---
mathjax: true
title: Educational Round 71 题解
tags:
  - Codeforces
  - Solution
  - Combinatorial
  - String
  - KMP
  - Sqrt
  - Interaction
categories:
  - Codeforces
  - Educational Round
date: 2019-08-26 01:23:42
---

# A There Are Two Types Of Burgers

# B Square Filling

天道有轮回，$n,m$ 打反饶过谁？

# C Gas Pipeline

有一段长度为 $n$ 管道，给定一个 $01$ 编码，$1$ 表示这一段管道高度为 $2$，否则可以为 $1$ 或 $2$。

$n+1$ 个端点，每个端点上设置支架，$n$ 段管道，可能在某些位置被抬高，管道单价为 $a$，支架单价为 $b$，最小化总费用。

枚举中间的每一段低处是否被完全抬起费用更低。

# D Number Of Permutations

给定一个二元组的序列，求有多少种排列方式，使得两个二元组的两个维度均不单调不减。

容斥，总排列数减去分别单调和偏序单调的方案数，类似于多重集的排列。

# E XOR Guessing

有一个数字 $x$，询问两次，每次询问有 $100$ 个数，返回 $x$ 和某一个询问值的异或。

第一次，询问 $1$ 到 $100$，第二次询问 $1$ 到 $100$，每个乘上 $2^7$，即第一次询问低 $7$ 位，第二次询问高 $7$ 位。

# F Remainder Problem

单点更新，询问模 $x$ 余 $y$ 所有位置的和。

根号分块，模数小的，维护答案，模数大的，有贡献的位置不超过 $\sqrt{n}$。

# G Indie Album

输入一个 Trie，每次询问一个输入串 $T$ 作为 Trie 上的第 $i$ 个结点的子串出现次数。

考虑都是一个串的情况，可以枚举母串的前缀在询问串 AC 自动机上的匹配结点，如果匹配点在 $fail$ 树上，到根的路径里经过询问串终点，那么当前前缀的一个后缀是询问串。

多串的情况就是在原 Trie 上 $dfs$，对询问串建 AC 自动机，维护 AC 自动机的匹配结点，单点更新，子树查询和。

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
 
int b, p, f, h, c;
 
int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d%d%d%d", &b, &p, &f, &h, &c);
        int ans = 0;
        if (h >= c) {
            int mn = min(b / 2, p);
            ans += mn * h;
            b -= mn * 2;
            mn = min(b / 2, f);
            ans += mn * c;
        } else {
            int mn = min(b / 2, f);
            ans += mn * c; 
            b -= mn * 2;
            mn = min(b / 2, p);
            ans += mn * h;
        }
        printf("%d\n", ans);
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
const int maxn = 100 + 5;
 
int n, m, mp[maxn][maxn], vis[maxn][maxn];
 
int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            scanf("%d", &mp[i][j]);
        }
    }
    vector<PII> ans;
    for (int i = 1; i + 1 <= n; i++) {
        for (int j = 1; j + 1 <= m; j++) {
            if (mp[i][j] && mp[i + 1][j] && mp[i][j + 1] && mp[i + 1][j + 1]) {
                ans.push_back({i, j});
                vis[i][j] = vis[i + 1][j] = vis[i][j + 1] = vis[i + 1][j + 1] = 1;
            }
        }
    }
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            if (mp[i][j]) {
                if (!vis[i][j]) return puts("-1"), 0;
            }
        }
    }
    printf("%d\n", (int)ans.size());
    for (auto x: ans) printf("%d %d\n", x.first, x.second);
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
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 200000 + 5;
 
int n, a, b;
char s[maxn];
 
int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d%d%s", &n, &a, &b, s + 1);
        ll res = 1ll * (n + 1) * b + 1ll * n * a;
        // dbg(res);
        int last = -1;
        for (int i = 1; i <= n; i++) {
            if (s[i] == '0') continue;
            if (last != -1) {
                // dbg(last, i);
                int len = i - last - 1;
                if (1ll * len * b <= 2ll * a) {
                    res -= 2ll * a;
                    res += 1ll * len * b;
                }
            }
            int j = i;
            while (j <= n && s[j] == '1') j++;
            res += 1ll * (j - i + 1) * b + 2ll * a;
            // dbg(i, j, 1ll * (j - i + 1) * b + 2ll * a);
            last = j; i = j - 1;
        }
        printf("%lld\n", res);
    }
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
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;
 
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
 
int n, f[maxn];
PII a[maxn];
 
int main() {
    f[0] = 1;
    scanf("%d", &n);
    map<int,int> mpa, mpb;
    for (int i = 1; i <= n; i++) {
        scanf("%d%d", &a[i].first, &a[i].second);
        mpa[a[i].first]++;
        mpb[a[i].second]++;
        f[i] = mul(f[i - 1], i);
    }
    int ans = f[n], tot = 1;
    for (auto& x: mpa) tot = mul(tot, f[x.second]);
    ans = sub(ans, tot);
    tot = 1;
    for (auto& x: mpb) tot = mul(tot, f[x.second]);
    ans = sub(ans, tot);
 
    sort(a + 1, a + 1 + n);
    int flag = 1;
    for (int i = 2; i <= n; i++) {
        if (a[i].first < a[i - 1].first || a[i].second < a[i - 1].second) {
            flag = 0; break;
        }
    }
    if (flag) {
        map<PII,int> mp;
        for (int i = 1; i <= n; i++) {
            mp[a[i]]++;
        }
        tot = 1;
        for (auto& x: mp) tot = mul(tot, f[x.second]);
        ans = add(ans, tot);
    }
 
    printf("%d", ans);
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
#include <cassert>
#include <set>
#include <map>
#include <random>
#include <ctime>
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
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = (1 << 14) - 1;
 
int query(vector<int> v) {
    cout << "?";
    assert((int)v.size() == 100);
    for (int x: v) cout << " " << x;
    cout << endl;
    int x; cin >> x;
    if (x == -1) exit(0);
    return x;
}
 
int main() {
    vector<int> v1, v2;
    for (int i = 1; i <= 100; i++) v1.push_back(i);
    for (int i = 1; i <= 100; i++) v2.push_back(i << 7);
    int r1 = query(v1);
    int r2 = query(v2);
    map<int,int> mp;
    for (int x: v1) mp[x ^ r1]++;
    for (int x: v2) mp[x ^ r2]++;
    for (auto x: mp) {
        if (x.second == 2) {
            // dbg(x.first);
            cout << "! " << x.first << endl;
            return 0;
        }
    }
    assert(0);
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
const int inf = 1 << 30;
const int maxn = 500000 + 5;
const int sz = 800;
 
int q, block[sz + 5][sz + 5], a[maxn];
 
int main() {
    scanf("%d", &q);
    int op, x, y;
    while (q--) {
        scanf("%d%d%d", &op, &x, &y);
        if (op == 1) {
            for (int i = 1; i <= sz; i++) block[i][x % i] += y;
            a[x] += y;
        } else {
            if (x <= sz) printf("%d\n", block[x][y]);
            else {
                int ans = 0;
                for (int i = y; i < maxn; i += x) ans += a[i];
                printf("%d\n", ans);
            }
        }
    }
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
#include <functional>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 500000 + 5;
 
struct BIT {
    int a[maxn];
    void update(int i, int x) {
        for (; i < maxn; i += i & -i) a[i] += x;
    }
    int query(int i) {
        int r = 0;
        for (; i; i -= i & -i) r += a[i];
        return r;
    }
    int query(int l, int r) {
        return query(r) - query(l - 1);
    }
} g;
 
namespace ACAM {
    static const int maxp = 500000 + 5;
    int sz, ch[maxp][26], fail[maxp];
    int node() {
        ms(ch[++sz], 0); fail[sz] = 0;
        return sz;
    }
    void clear() {
        sz = 0; node();
        for (int i = 0; i < 26; i++) ch[0][i] = 1;
    }
    int insert(char* s) {
        int u = 1;
        for (int i = 0; s[i]; i++) {
            int v = s[i] - 'a';
            if (!ch[u][v]) ch[u][v] = node();
            u = ch[u][v];
        }
        return u;
    }
    vector<int> edge[maxp];
    int tin[maxp], tout[maxp];
    void build() {
        queue<int> q; q.push(1);
        while (!q.empty()) {
            int t = q.front(); q.pop();
            for (int i = 0; i < 26; i++) {
                if (ch[t][i]) {
                    fail[ch[t][i]] = ch[fail[t]][i];
                    q.push(ch[t][i]);
                } else {
                    ch[t][i] = ch[fail[t]][i];
                }
            }
        }
        for (int i = 2; i <= sz; i++) edge[fail[i]].push_back(i);
        int tot = 0;
        function<void(int)> dfs = [&](int u) {
            tin[u] = ++tot;
            for (int v: edge[u]) {
                dfs(v);
            }
            tout[u] = tot;
        };
        dfs(1);
    }
}
using ACAM::ch;
using ACAM::tin;
using ACAM::tout;
 
int n, q, ans[maxn];
int trie[maxn][26], sz = 1, pos[maxn];
char s[maxn];
 
vector<PII> que[maxn];
void dfs(int u, int x) {
    g.update(tin[x], 1);
    for (auto q: que[u]) {
        int v = q.first, id = q.second;
        ans[id] = g.query(tin[v], tout[v]);
    }
    for (int c = 0; c < 26; c++) {
        if (!trie[u][c]) continue;
        int v = trie[u][c];
        dfs(v, ACAM::ch[x][c]);
    }
    g.update(tin[x], -1);
}
 
int main() {
    ACAM::clear();
    scanf("%d", &n);
    for (int i = 1, op, fa; i <= n; i++) {
        scanf("%d", &op);
        char s[5];
        if (op == 1) {
            scanf("%s", s);
            fa = 1;
        } else {
            scanf("%d%s", &fa, s);
            fa = pos[fa];
        }
        int c = s[0] - 'a';
        if (!trie[fa][c]) trie[fa][c] = ++sz;
        pos[i] = trie[fa][c];
    }
    scanf("%d", &q);
    for (int i = 1, t; i <= q; i++) {
        scanf("%d%s", &t, s);
        int u = ACAM::insert(s);
        que[pos[t]].push_back({u, i});
    }
    ACAM::build();
    dfs(1, 1);
    for (int i = 1; i <= q; i++) printf("%d\n", ans[i]);
    return 0;
}
```
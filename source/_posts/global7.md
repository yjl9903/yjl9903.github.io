---
mathjax: true
title: Codeforces Global Round 7 题解
date: 2020-03-20 16:26:11
tags:
  - Codeforces
  - Solution
  - String
  - Palindrome
  - Data Structure
  - Match
  - SegTree
  - DP
  - Math
  - Poly
categories:
  - Codeforces
---

# A Bad Ugly Numbers

输出 $233333333$。

# B Maximums

注意到 $a_1=b_1$，扫一遍。

# C Permutation Partitions

给定一个排列，将其分为 $k$ 个连续段，每段的权值是该段的最大值，求划分的最大权值和方案数。

显然就是取最大 $k$ 个即可，划分就是枚举分界点，方案数乘起来即可。

# D Prefix-Suffix Palindrome

给定一个串 $s$，找一个最长的回文串 $p$，使得 $p=a+b$，其中 $a$ 为 $s$ 的前缀，$b$ 为 $s$ 的后缀。

枚举前后缀的相同的长度，在中间找一个以某个位置开头或结尾的长度小于一个值的最长回文串，抄一个回文树就赢了。

# E Bombs

给定一个排列 $p$，有另外一个排列 $q$，对于 $q$ 的每个前缀，将对应位置设置上炸弹，求对应的权值。

维护一个集合 $S$，从左到右扫描排列 $p$，加入集合，如果是炸弹就删除集合的最大值，最后留下来的最大值就是权值。

显然，答案是单调不增的。因此，只需要检查答案能否减小。

答案减小必定是炸掉最大的那些数字。如果我们无视一些炸弹的顺序，实际上对于要被炸掉的关键位置，就是和它之后的所有炸弹连边。这样建图后，就是判断是否存在完美匹配。

考虑 Hall 定理，判断条件等价于，对于最后一个位置，其后缀炸弹总数大于等于 $1$，倒数第二个后缀炸弹总数大于等于 $2$，以此类推。

使用线段树维护后缀和的最小值即可。

# F Wise Men

给定一个 $n$ $(2 \le n \le 18)$ 个点的无向图，一个排列可以生成一个 $0/1$ 串，如果第 $i$ 个和第 $i+1$ 个有边则是 $1$ 否则为 $0$。对于所有长度为 $n-1$ 的 $0/1$ 串，求它对应多少个排列。

首先将问题转化为高维后缀和，令 $f(S)$ 表示状态为 $S$ 的方案数，其中 $S$ 内的 $1$ 表示有边，$0$ 表示无边或有边。最后做一个高维后缀和的差分就能弄出真实答案。

注意到一个结论，对于一个状态 $S=110111$ 只需要看每个 $1$ 连续块的 $1$ 个数加一的多重集合，这里是 $\{ 3, 4 \}$（中间那个连接处没有考虑，因此也可能是 $111111$）。

因此只需要枚举 $n$ 的整数划分方案就能搞出每个 $f(S)$ 的值，并且注意到 $P(18)=385$。

状压 DP 搞出每个子集作为链的方案数，令状态是 $dp(i,S)$ 表示链头为 $i$，点集为 $S$ 的方案数，时间复杂度 $O(2^nn^2)$。

记全集为 $U$，$g(i,S)$ 表示选出点集 $S$，$i=|S|$ 的方案数，我们要求的是

$$f(S)=\sum \prod_{i=1}^k g(|s_i|,s_i)$$

其中 $ \sum_{i=1}^k |s_i| = n, \cup_{i=1}^k |s_i| =U $，实际上 $ \{ |s_1|, |s_2|, \dots, |s_k| \} $ 就是 $S$ 对应的整数划分方案。同时，这个卷积的式子也保证了一个点不会出现多次，因为一个点出现多次的话，必定不能满足整数划分的条件。

枚举到一个整数划分 $\{ a_1, a_2, \dots, a_k \}$ 后，也就是对 $g(a_1,0 \dots 2^n), g(a_2, 0 \dots 2^n), \dots$ 全部做一个子集或卷积。预处理 $g(1), g(2), \dots$ 的点值后，在搜索时顺便乘起来，在枚举完后就得到上面整个卷积的点值表示 $A$。由于最后我们只需要 $U$ 处的系数，可以直接使用 $O(2^n)$ 的容斥得到 $U$ 处的系数。

$$
FWT^{-1}(A)[x^U] = \sum_{s \in U} (-1)^{ |s \oplus U| } A[x^s]
$$

搜完整数划分，求出每个点集 $S$ 的 $f(S)$ 值后，做一个高维差分，把高维后缀和转化为真实值即可。

时间复杂度：$O(2^n(P(n)+n^2))$，其中 $P(n)$ 表示 $n$ 的整数划分方案数。

一个优化常数的建议：搜整数划分时，剪掉不合法的分支，不去乘点值。

<!--more-->

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 200000 + 5;

int n;

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d", &n);
    if (n == 1) {
      puts("-1");
    } else {
      if ((2 * n + 1) % 3) {
        n--;
        while (n--) putchar('2');
        puts("3");
      } else {
        n -= 2;
        while (n--) putchar('2');
        puts("43");
      }
    }
  }
  return 0;
}
```

## B

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 200000 + 5;

int n, b[maxn];
ll a[maxn];

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d", b + i);
  }
  a[1] = b[1];
  ll mx = a[1];
  for (int i = 2; i <= n; i++) {
    a[i] = b[i] + mx;
    mx = max(mx, a[i]);
  }
  for (int i = 1; i <= n; i++) {
    printf("%I64d ", a[i]);
  }
  puts("");
  return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 200000 + 5;

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

int n, k, a[maxn];

int main() {
  scanf("%d%d", &n, &k);
  vector<PII> v;
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
    v.emplace_back(a[i], i);
  }
  sort(begin(v), end(v), greater<PII>());
  vector<int> pos;
  ll sum = 0;
  int ans = 1;
  for (int i = 0; i < k; i++) pos.push_back(v[i].second), sum += v[i].first;
  sort(begin(pos), end(pos));
  for (int i = 1; i < k; i++) {
    ans = mul(ans, pos[i] - pos[i - 1]);
  }
  printf("%I64d %d", sum, ans);
  return 0;
}
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#ifdef XLor
  #define dbg(args...) cout << "\033[32;1m" << #args << " -> ", err(args)
  void err() { std::cout << "\033[39;0m" << std::endl; }
  template<typename T, typename...Args>
  void err(T a, Args...args) { std::cout << a << ' '; err(args...); }
#else
  #define dbg(...)
#endif
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 1000000 + 5;

char s[maxn];
int n;

struct pam {
  int sz, tot, last;
  int ch[maxn][26], len[maxn], fail[maxn];
  int dif[maxn], slink[maxn];;
  char s[maxn];
  int node(int l) {
    sz++; ms(ch[sz], 0);
    len[sz] = l; fail[sz] = 0;
    return sz;
  }
  void clear() {
    sz = -1; last = 0;
    s[tot = 0] = '$';
    node(0); node(-1);
    fail[0] = 1;
  }
  int getfail(int x) {
    while (s[tot - len[x] - 1] != s[tot]) x = fail[x];
    return x;
  }
  int insert(char c) {
    s[++tot] = c;
    int now = getfail(last);
    if (!ch[now][c - 'a']) {
      int x = node(len[now] + 2);
      fail[x] = ch[getfail(fail[now])][c - 'a'];
      ch[now][c - 'a'] = x;

      dif[x] = len[x] - len[fail[x]];
      if (dif[x] == dif[fail[x]]) slink[x] = slink[fail[x]];
      else slink[x] = fail[x];
    }
    last = ch[now][c - 'a'];
    return last;
  }
  int get(int u, int ql) {
    if (ql > len[u]) return len[u];
    while (true) {
      int l = len[slink[u]];
      int d = dif[u];
      if (ql >= l) {
        return ((ql - l) / d) * d + l;
      }
      u = slink[u];
    }
    return 0;
  }
} f, g;

int pos1[maxn], pos2[maxn];

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%s", s + 1);
    n = strlen(s + 1);

    int ans = 1, plen = 1, slen = 0;

    f.clear(); g.clear();
    for (int i = n; i >= 1; i--) {
      int u = f.insert(s[i]);
      pos1[i] = u;
      if (i == 1) {
        ans = f.len[u];
        plen = ans;
        slen = 0;
      }
    }
    for (int i = 1; i <= n; i++) {
      int u = g.insert(s[i]);
      pos2[i] = u;
      if (i == n) {
        if (g.len[u] > ans) {
          ans = g.len[u];
          plen = 0;
          slen = ans;
        }
      }
    }

    for (int i = 1; i + i <= n; i++) {
      if (s[i] == s[n - i + 1]) {
        if (n == i + i) {
          ans = n;
          plen = n;
          slen = 0;
          break;
        }

        int tot = f.get(pos1[i + 1], n - i - i);
        if (i + i + tot > ans) {
          ans = i + i + tot;
          plen = i + tot;
          slen = i;
        }

        tot = g.get(pos2[n - i], n - i - i);
        if (i + i + tot > ans) {
          ans = i + i + tot;
          plen = i;
          slen = i + tot;
        }
      } else {
        break;
      }
    }
    for (int i = 1; i <= plen; i++) {
      putchar(s[i]);
    }
    for (int i = n - slen + 1; i <= n; i++) {
      putchar(s[i]);
    }
    puts("");
  }
  return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#ifdef XLor
  #define dbg(args...) cout << "\033[32;1m" << #args << " -> ", err(args)
  void err() { std::cout << "\033[39;0m" << std::endl; }
  template<typename T, typename...Args>
  void err(T a, Args...args) { std::cout << a << ' '; err(args...); }
#else
  #define dbg(...)
#endif
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

int n, p[maxn], q[maxn], pos[maxn];

struct SegTree {
  #define lson l, m, rt << 1
  #define rson m + 1, r, rt << 1 | 1
  function<ll(ll,ll)> merge = [](ll a, ll b) { return min(a, b); };
  ll a[maxn << 2];
  int tag[maxn << 2];
  void build(int l = 1, int r = n, int rt = 1) {
    if (l == r) {
      a[rt] = 0; return ;
    }
    int m = (l + r) / 2;
    build(lson); build(rson);
    a[rt] = merge(a[rt << 1], a[rt << 1 | 1]);
  }
  void push(int rt, int x) {
    a[rt] += x; tag[rt] += x;
  }
  void pushdown(int rt) {
    if (!tag[rt]) return ;
    push(rt << 1, tag[rt]);
    push(rt << 1 | 1, tag[rt]);
    tag[rt] = 0;
  }
  void update(int L, int R, int x, int l = 1, int r = n, int rt = 1) {
    if (L <= l && r <= R) {
      push(rt, x); return ;
    }
    int m = (l + r) / 2; pushdown(rt);
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    a[rt] = merge(a[rt << 1], a[rt << 1 | 1]);
  }
  ll query(int L, int R, int l = 1, int r = n, int rt = 1) {
    if (L <= l && r <= R) return a[rt];
    int m = (l + r) / 2; pushdown(rt);
    if (R <= m) return query(L, R, lson);
    if (L > m) return query(L, R, rson);
    return merge(query(L, R, lson), query(L, R, rson));
  }
} f;

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d", p + i);
    pos[p[i]] = i;
  }
  for (int i = 1; i <= n; i++) {
    scanf("%d", q + i);
  }
  int ans = n;
  auto check = [&]() {
    if (ans == 1) return false;
    f.update(1, pos[ans], -1);
    if (f.query(1, n) < 0) {
      f.update(1, pos[ans], 1);
      return false;
    }
    ans--;
    return true;
  };
  for (int i = 1; i <= n; i++) {
    printf("%d ", ans);
    f.update(1, q[i], 1);
    while (check());
  }
  return 0;
}
```

## F

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#ifdef XLor
  #define dbg(args...) cout << "\033[32;1m" << #args << " -> ", err(args)
  void err() { std::cout << "\033[39;0m" << std::endl; }
  template<typename T, typename...Args>
  void err(T a, Args...args) { std::cout << a << ' '; err(args...); }
#else
  #define dbg(...)
#endif
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 19;

int n;
ll chain[maxn][1 << maxn], suml[maxn][1 << maxn], ans[1 << maxn];
bool g[maxn][maxn];

int main() {
  scanf("%d", &n);
  for (int i = 0; i < n; i++) {
    char s[20]; scanf("%s", s);
    for (int j = 0; j < n; j++) {
      g[i][j] = s[j] - '0';
    }
    chain[i][1 << i] = 1;
  }
  int ss = 1 << n;
  for (int s = 0; s < ss; s++) {
    for (int i = 0; i < n; i++) {
      if (s >> i & 1) {
        for (int j = 0; j < n; j++) {
          if (i == j || !g[i][j]) continue;
          if (s >> j & 1) {
            chain[i][s] += chain[j][s ^ (1 << i)];
          }
        }
        suml[__builtin_popcount(s)][s] += chain[i][s];
      }
    }
  }
  for (int len = 1; len <= n; len++) {
    for (int i = 0; i < n; i++) {
      for (int s = 0; s < ss; s++) {
        if (s & (1 << i)) {
          suml[len][s] += suml[len][s - (1 << i)];
        }
      }
    }
  }
  map<vector<int>, vector<int> > pat;
  for (int s = 0; s < ss / 2; s++) {
    vector<int> v;
    int tot = 0;
    for (int i = 0; i < n - 1; i++) {
      if (s >> i & 1) {
        tot++;
      } else {
        v.push_back(tot + 1);
        tot = 0;
      }
    }
    v.push_back(tot + 1);
    sort(begin(v), end(v));
    pat[v].push_back(s);
  }
  vector<int> stk;
  function<void(int,int,vector<ll>)> dfs = [&](int n, int st, vector<ll> g) {
    if (n == 0) {
      ll sum = 0;
      for (int s = 0; s < ss; s++) {
        if (__builtin_popcount(s ^ (ss - 1)) % 2 == 0) {
          sum += g[s];
        } else {
          sum -= g[s];
        }
      }
      for (auto s: pat[stk]) {
        ans[s] = sum;
      }
      return ;
    }
    for (int i = st; i <= n; i++) {
      if (i != n && i + i > n) continue;
      stk.push_back(i);
      auto tot = g;
      for (int s = 0; s < ss; s++) {
        tot[s] *= suml[i][s];
      }
      dfs(n - i, i, tot);
      stk.pop_back();
    }
  };
  dfs(n, 1, vector<ll>(1 << n, 1));
  for (int i = 0; i < (n - 1); i++) {
    for (int s = 0; s < ss / 2; s++) {
      if (((s >> i) & 1) == 0) {
        ans[s] -= ans[s + (1 << i)];
      }
    }
  }
  for (int s = 0; s < ss / 2; s++) {
    printf("%I64d ", ans[s]);
  }
  return 0;
}
```

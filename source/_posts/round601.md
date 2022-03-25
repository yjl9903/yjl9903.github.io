---
mathjax: true
title: Codeforces Round 601 题解
tags:
  - Codeforces
  - Solution
  - Tree
  - Data Structure
  - Interaction
  - Math
  - DP
categories:
  - Codeforces
date: 2019-11-20 09:16:26
---

Div. 1 (久违的题解)。

# A Feeding Chicken

给定一块 $r \times c$ 的空地，每个格子上可能放着一份猫粮，一共 $k$ 只猫，你现在需要为每只猫分配一个四联通块，使得每只猫得到的猫粮个数极差最小。

显然，可以尽量构造出一种平均的方案，即极差 $\le 1$。一个比较容易的方案是按照蛇形棋的顺序分配。

# B Send Boxes to Alice

给定一个序列 $a$，每个位置有 $a_i$ 份猫粮，每次操作可以把一份猫粮从 $i$ 位置移动到 $i+1$ 或 $i-1$，你现在要最小化操作次数，使得整个序列的 $\gcd > 1$。

枚举总猫粮数的因子 $p$，计算使得最终 $\gcd$ 为 $p$ 的倍数时的最小操作次数。枚举每个前缀，计算在模 $p$ 意义下的前缀和 $sum$，代表前面这些位置还未被分配的个数，如果这些个数很少，可以把这些东西统一向后移动一格，否则个数很多时，可以把后面的东西移动到前边，即 $\min(sum,p-sum)$。

# C Point Ordering

交互题，猜一个 $n$ 个点凸多边形的顺序，隐藏多边形的点编号不是按照顺时针或逆时针顺序，而是乱序，你现在需要用最多 $3n$ 次询问猜出这个顺序。

你有两种询问，第一种询问一个三角形的面积，第二种询问三个点 $i,j,k$ 的 $\vec{ij} \times \vec{ik}$ 的正负号。

第一步，用 $n$ 次询问找出多边形的一条边。随便选一对点，然后枚举其他点向某一边扩展。

第二步，求出这条边和其他所有点的三角形面积，找到面积最大的三角形的对应顶点。

第三步，确定其他点在上一步这个最大顶点的哪一侧。同一侧高度单调，面积也单调。

# D Tree Queries

给定一棵无根树，有两种操作。

1. 输入 $v,d$，从 $n$ 个点中等概率选择点 $r$，然后将所有满足到 $r$ 的路径经过点 $v$ 的点 $u$，权值加上 $d$。

2. 询问 $v$ 的点权期望。

简单推一下修改的贡献，点 $u$ 的期望加 $d$，考虑其每个邻居（包括父亲）的子树大小 $siz$，这个子树内的点权加上 $(n-siz)/n$。

然后一个显然的做法呼之欲出，按度数大小分块，小度数线段树维护，大度数暴力打标记，时间复杂度 $O(n\sqrt{n \log n})$。

修改的瓶颈在于不能枚举一个点的所有度数。考虑树剖，只修改重儿子对应子树，和询问点的子树外的所有点。询问时，只需要计算根到询问点路径上所有轻边带来的贡献，显然只有 $\log n$ 条轻边，时间复杂度 $O(n\log n)$。

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
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 500 + 5;

char gc(int x) {
  if (x < 26) return x + 'a';
  else if (x < 52) return x - 26 + 'A';
  else return x - 52 + '0';
  return '.';
}

int r, c, k;
char s[maxn][maxn], ans[maxn][maxn];

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d%d%d", &r, &c, &k);
    int sum = 0;
    for (int i = 1; i <= r; i++) {
      scanf("%s", s[i] + 1);
      for (int j = 1; j <= c; j++) {
        if (s[i][j] == 'R') sum++;
        ans[i][j] = 0;
      }
    }
    vector<int> num(k, sum / k);
    for (int i = sum / k * k + 1, j = 0; i <= sum; i++, j++) {
      num[j]++;
    }
    int p = 0;
    for (int i = 1; i <= r; i++) {
      if (i % 2 == 1) {
        for (int j = 1; j <= c; j++) {
          ans[i][j] = gc(p);
          if (s[i][j] == 'R') num[p]--;
          if (p + 1 < k && num[p] == 0) p++;
        }
      } else {
        for (int j = c; j >= 1; j--) {
          ans[i][j] = gc(p);
          if (s[i][j] == 'R') num[p]--;
          if (p + 1 < k && num[p] == 0) p++;
        }
      }
    }
    for (int i = 1; i <= r; i++) {
      for (int j = 1; j <= c; j++) {
        putchar(ans[i][j]);
      }
      puts("");
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
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const ll inf = 1ll << 62;
const int maxn = 1000000 + 5;

int n, a[maxn];

ll calc(ll x) {
  ll pre = 0, ans = 0;
  for (int i = 1; i <= n; i++) {
    pre = (pre + a[i]) % x;
    ans += min(pre, x - pre);
  }
  return ans;
}

int main() {
  scanf("%d", &n);
  ll sum = 0;
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
    sum += a[i];
  }
  ll ans = inf;
  for (int i = 2; 1ll * i * i <= sum; i++) {
    if (sum % i) continue;
    ans = min(ans, calc(i));
    while (sum % i == 0) sum /= i;
  }
  if (sum > 1) ans = min(ans, calc(sum));
  if (ans == inf) puts("-1");
  else printf("%I64d\n", ans);
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
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 1000 + 5;

ll qs(int i, int j, int k) {
  cout << 1 << ' ' << i << ' ' << j << ' ' << k << endl;
  ll sum; cin >> sum; return sum;
}
int qd(int i, int j, int k) {
  cout << 2 << ' ' << i << ' ' << j << ' ' << k << endl;
  cin >> i; return i;
}

int n, dir[maxn];

int main() {
  ios::sync_with_stdio(false);
  cin >> n;
  int eg = 2;
  // find an edge
  for (int i = 3; i <= n; i++) {
    int d = qd(1, eg, i);
    if (d > 0) eg = i;
  }

  // sort all the tri
  vector< pair<ll,int> > v;
  for (int i = 2; i <= n; i++) {
    if (i == eg) continue;
    v.emplace_back(qs(1, eg, i), i);
  }
  sort(begin(v), end(v));

  // left or right
  int mxe = v.back().second;
  for (int i = 2; i <= n; i++) {
    if (i == mxe) continue;
    dir[i] = qd(1, mxe, i);
  }

  vector<int> ans1, ans2;
  for (auto& x: v) {
    int i = x.second;
    if (dir[i] > 0) {
      ans1.push_back(i);
    } else if (dir[i] < 0) {
      ans2.push_back(i);
    }
  }
  vector<int> ans(1, 1);
  for (int x: ans2) ans.push_back(x);
  ans.push_back(mxe);
  reverse(begin(ans1), end(ans1));
  for (int x: ans1) ans.push_back(x);
  ans.push_back(eg);
  cout << "0";
  for (int x: ans) cout << ' ' << x;
  cout << endl;
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
const int maxn = 150000 + 5;

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

int n, q, son[maxn], siz[maxn], dfn[maxn], tot, ivn;
int top[maxn], fa[maxn], val[maxn];
vector<int> edge[maxn];

struct BIT {
  int a[maxn];
  void update(int i, int x) {
    for (; i <= n; i += i & -i) a[i] = add(a[i], x);
  }
  void update(int l, int r, int x) {
    if (l >= r) return ;
    update(l, x); update(r, mod - x);
  }
  int query(int i) {
    int r = 0;
    for (; i; i -= i & -i) r = add(r, a[i]);
    return r;
  }
} h;

void dfs(int u, int f) {
  fa[u] = f;
  dfn[u] = ++tot; siz[u] = 1;
  for (int v: edge[u]) {
    if (v == f) continue;
    dfs(v, u); siz[u] += siz[v];
    if (siz[v] > siz[son[u]]) son[u] = v;
  }
}
void dfs(int u, int f, int tp) {
  top[u] = tp;
  if (son[u]) dfs(son[u], u, tp);
  for (int v: edge[u]) {
    if (v == f || v == son[u]) continue;
    dfs(v, u, v);
  }
}

void update(int u, int d) {
  val[u] = add(val[u], d);
  int tot = mul(d, siz[u]);
  h.update(1, dfn[u], tot);
  h.update(dfn[u] + siz[u], n + 1, tot);
  if (son[u]) {
    int v = son[u]; tot = mul(d, n - siz[v]);
    h.update(dfn[v], dfn[v] + siz[v], tot);
  }
}

int query(int u) {
  int ans = h.query(dfn[u]), x = u;
  while (x) {
    int tp = top[x];
    ans = add(ans, mul(val[fa[tp]], n - siz[tp]));
    x = fa[tp];
  }
  return add(val[u], mul(ans, ivn));
}

int main() {
  scanf("%d%d", &n, &q);
  ivn = inv(n);
  for (int i = 2, u, v; i <= n; i++) {
    scanf("%d%d", &u, &v);
    edge[u].push_back(v);
    edge[v].push_back(u);
  }
  dfs(1, 0); dfs(1, 0, 1);
  int op, u, d;
  while (q--) {
    scanf("%d%d", &op, &u);
    if (op == 1) {
      scanf("%d", &d);
      update(u, d);
    } else if (op == 2) {
      printf("%d\n", query(u));
    }
  }
  return 0;
}
```
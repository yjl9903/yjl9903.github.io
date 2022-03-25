---
mathjax: true
title: Codeforces Round 709 题解
tags:
  - Codeforces
  - Solution
  - String
categories:
  - Codeforces
date: 2021-03-25 01:56:43
---

# B Playlist

给定一个环形的数组 $a_1, a_2, \dots, a_n$，有一个指针 $p$ 在环上进行扫描，如果当前位置和上一个位置的数 $\gcd = 1$，那么删除当前位置，指针移到下一个位置重新开始，直到无法删除。

我们不能暴力模拟，因为每次可能会跳 $O(n)$ 次来找到目标删除的位置。例如：$2 \times 3, 3 \times 5, \dots, p$，每次会删除第一个数。

因此，我们需要优化这个找删除位置的过程。注意到，记 $\text{next}(i)$ 表示在剩下的位置中 $i$ 后面的那一个（环的意义下）。如果 $\gcd(a[i], a[\text{next}(i)]) > 1 $，那么这个 $i$ 位置和他后面的这一对数，在之后永远不会被当作一对删除，因此我们在找删除对的时候，不再需要考虑位置 $i$。因为，我们在不会跳过 $i$ 来删除 $i$ 后面那个，$i$ 后面那个会一直存在，直到 $i$ 被前面的删除，导致其变换前驱。

# C Skyline Photo

给定一个排列 $h_1, h_2, \dots, h_n$，将这个数组划分为多个段，每个段的权值是 $h$ 的最小位置 $i$ 的权值 $b_i$，总权值是所有权值的和，求最大权值。

显然，如果权值都是正数那么只要分成长度为 $1$ 的段即可。考虑 $f(i)$ 表示以 $i$ 结尾的最大权值，那么这个权值会一直存活到下一个 $h$ 比它小的位置，这部分维护一个扫描线。然后考虑 $f(i)$ 是怎么得到的，会有两部分转移一部分是从上一个比它小的位置到这个位置的一段区间中取一个最大的，或者是从扫描线中取一个最大的。

# D Useful Edges

给定一个带边权的无向图和 $q$ 组条件三元组 $(u,v,l)$。对于边 $(a,b)$，如果存在三元组 $(u,v,l)$ 和一条 $u \to v$ 的路径，满足这条路径权值和小于等于 $l$，并且 $(a,b)$ 在这条路径上，那么这条边是好边。求好边个数。

跑一下 Floyd 之后，对于一条权值为 $w$ 边 $(a,b)$，把条件列出来，存在 $1 \le i \le q$，满足 $dis(u_i,a)+w+dis(b,v_i) \le l_i$。注意到条件三元组会有 $O(n^2)$，显然不能全部枚举来判断。

移项 $w + dis(b,v_i) \le l_i - dis(u_i, a)$，这个式子的左边固定一个端点 $b$，我们枚举另外一个端点 $v_i$，然后希望预处理右边的最大值来直接判断。此时对于式子的右边就是对于固定的端点 $a$，在所有点 $1 \le u \le n$ 中，取最大的 $l(u,v) - dis(u, a)$。我们提取 $v$ 和 $a$ 作为键，$u$ 是唯一的变元，因此枚举 $u$ 预处理最大值，即可直接判断。

# F Exam

给定 $n$ 个串，求有多对串 $(i,j)$ 满足 $s_i$ 是 $s_j$ 的子串，并且不存在 $k$ 使得 $s_i$ 是 $s_k$ 的子串，$s_k$ 是 $s_j$ 的子串。

首先，建出 AC 机，预处理每个点往上走第一个结束节点和 fail 树的 dfs 序。

我们枚举每一个串，计算它有多少个直接的子串。显然直接子串一定是，这个串所有前缀的最长后缀，满足这个后缀在字典中出现过，直接使用 AC 机预处理的信息容易得出（注意，完整串可能需要特判，因为完整串的最长后缀是其本身）。但是这些串中显然会有很多重复的，也会有很多串在另外的当中出现过。

我们结合 AC 机的结构，观察一下哪些串是重复的。显然每个最长后缀对应节点，在 fail 树上到根的路径都是出现过的，这些节点的串构成母串的所有子串。

但是，考虑下面这样的情况：

![image.png](https://i.loli.net/2021/03/25/NOZIXuz1KlTFeMD.png)

对于那个最长的绿串实际上被包含在蓝串内部，但是根据我们上述的做法，我们多统计了绿串。因为上述做法，只排除了后缀的情况，没有排除不是后缀而是内部的情况。

正确做法是，我们可以通过从右往左枚举右端点，记录单调下降的左端点，将所有左端点变化处作为询问的关键点，这样就能够排除串在内部的情况。

<!--more-->

# 代码

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

int n, a[maxn], nxt[maxn];

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d", &n);
    set<int> ok, rest;
    for (int i = 1; i <= n; i++) {
      scanf("%d", a + i);
      ok.insert(i);
      rest.insert(i);
    }
    vector<int> ans;
    int last = 0;
    while (!ok.empty() && !rest.empty()) {
      auto it = ok.lower_bound(last);
      if (it == ok.end()) {
        it = ok.begin();
      }
      if (!rest.count(*it)) {
        ok.erase(it);
        continue;
      }
      int x = *it;
      it = rest.upper_bound(*it);
      if (it == rest.end()) {
        it = rest.begin();
      }
      int y = *it;
      if (__gcd(a[x], a[y]) == 1) {
        ans.push_back(y);
        rest.erase(y);
        last = y + 1;
      } else {
        ok.erase(x);
      }
    }
    printf("%d", ans.size());
    for (int x: ans) printf(" %d", x);
    puts("");
  }
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

int n, h[maxn], b[maxn], pos[maxn], L[maxn], R[maxn];
vector<ll> evs[maxn];

namespace SegT {
  #define lson l, m, rt << 1
  #define rson m + 1, r, rt << 1 | 1

  ll a[maxn << 2];
  void update(int i, ll x, int l = 0, int r = n, int rt = 1) {
    if (l == r) {
      a[rt] = x;
      return ;
    }
    int m = (l + r) / 2;
    if (i <= m) update(i, x, lson);
    else update(i, x, rson);
    a[rt] = max(a[rt << 1], a[rt << 1 | 1]);
  }

  ll query(int L, int R, int l = 0, int r = n, int rt = 1) {
    if (L <= l && r <= R) return a[rt];
    int m = (l + r) / 2;
    if (R <= m) return query(L, R, lson);
    else if (L > m) return query(L, R, rson);
    else return max(query(L, R, lson), query(L, R, rson));
  }
}

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d", h + i);
    pos[h[i]] = i;
  }
  for (int i = 1; i <= n; i++) {
    scanf("%d", b + i);
  }
  set<int> st;
  for (int i = 1; i <= n; i++) {
    int p = pos[i];
    auto it = st.upper_bound(p);
    if (it != st.end()) {
      R[p] = *it;
    } else {
      R[p] = n + 1;
    }
    if (it != st.begin()) {
      it--;
      L[p] = *it;
    } else {
      L[p] = 0;
    }
    st.insert(p);
  }
  // for (int i = 1; i <= n; i++) {
  //   dbg(i, L[i], R[i]);
  // }
  multiset<ll> val;
  for (int i = 1; i <= n; i++) {
    ll cur = SegT::query(L[i], i - 1) + b[i];
    for (auto& x: evs[i]) {
      val.erase(val.find(x));
    }
    ll ans = cur;
    if (!val.empty()) {
      ans = max(ans, *val.rbegin());
    }
    SegT::update(i, ans);
    val.insert(cur);
    evs[R[i]].emplace_back(cur);
  }
  printf("%lld\n", SegT::query(n, n));
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const ll inf = 1ll << 60;
const int maxn = 600 + 5;

int n, m, q;
ll f[maxn][maxn], L[maxn][maxn], mn[maxn][maxn];
int uu[maxn * maxn], vv[maxn * maxn], ww[maxn * maxn];

int main() {
  scanf("%d%d", &n, &m);
  for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= n; j++) {
      f[i][j] = inf;
      L[i][j] = inf;
      mn[i][j] = inf;
    }
    f[i][i] = 0;
  }
  for (int i = 1; i <= m; i++) {
    int u, v, w;
    scanf("%d%d%d", &u, &v, &w);
    f[u][v] = f[v][u] = min(f[u][v], 1ll * w);
    uu[i] = u;
    vv[i] = v;
    ww[i] = w;
  }
  for (int k = 1; k <= n; k++) {
    for (int i = 1; i <= n; i++) {
      for (int j = 1; j <= n; j++) {
        f[i][j] = min(f[i][j], f[i][k] + f[k][j]);
      }
    }
  }
  scanf("%d", &q);
  for (int i = 1; i <= q; i++) {
    int u, v, l;
    scanf("%d%d%d", &u, &v, &l);
    L[u][v] = L[v][u] = -1ll * l;
  }
  for (int x = 1; x <= n; x++) {
    for (int u = 1; u <= n; u++) {
      for (int v = 1; v <= n; v++) {
        mn[x][u] = min(mn[x][u], f[x][v] + L[u][v]);
      }
    }
  }
  int ans = 0;
  for (int i = 1; i <= m; i++) {
    int x = uu[i], y = vv[i], w = ww[i];
    for (int i = 1; i <= n; i++) {
      if (mn[x][i] <= -w - f[y][i]) {
        ans++;
        break;
      }
    }
  }
  printf("%d\n", ans);
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
#include <set>
#include <queue>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
const int maxn = 1000000 + 5;

namespace acam {
  static const int maxp = 1000000 + 5;
  static const int S = 26;
  static const int Base = 'a';
  int sz, ch[maxp][S], fail[maxp], val[maxp], link[maxn];
  int tot, tin[maxp], tout[maxp];
  vector<int> edge[maxp];
  int node() {
    ms(ch[++sz], 0);
    fail[sz] = val[sz] = 0;
    return sz;
  }
  void clear() {
    sz = 0; node();
    for (int i = 0; i < S; i++) ch[0][i] = 1;
  }
  int insert(char* s, int id) {
    int u = 1;
    for (int i = 0; s[i]; i++) {
      int v = s[i] - Base; // !
      if (!ch[u][v]) ch[u][v] = node();
      u = ch[u][v];
    }
    val[u] = id;
    return u;
  }
  void dfs(int u) {
    tin[u] = ++tot;
    for (int v: edge[u]) {
      link[v] = val[v] > 0 ? v : link[u];
      dfs(v);
    }
    tout[u] = tot;
  }
  void build() {
    queue<int> q; q.push(1);
    while (!q.empty()) {
      int t = q.front(); q.pop();
      for (int i = 0; i < S; i++) {
        if (ch[t][i]) {
          fail[ch[t][i]] = ch[fail[t]][i];
          q.push(ch[t][i]);
        } else {
          ch[t][i] = ch[fail[t]][i];
        }
      }
    }
    for (int i = 2; i <= sz; i++) {
      edge[fail[i]].push_back(i);
    }
    link[1] = -1;
    dfs(1);
  }
}

struct Bit {
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
} f;

int n, pre[maxn], lnk[maxn];
char buf[maxn];

int main() {
  acam::clear();
  scanf("%d", &n);
  vector<string> allS { "" };
  for (int i = 1; i <= n; i++) {
    scanf("%s", buf);
    acam::insert(buf, i);
    allS.emplace_back(buf);
  }
  acam::build();
  int ans = 0;
  for (auto& str: allS) {
    for (int i = 0, u = 1; i < str.length(); i++) {
      u = acam::ch[u][str[i] - 'a'];
      pre[i + 1] = u;
      if (i + 1 < str.length()) {
        int p = acam::link[u];
        lnk[i + 1] = p;
      } else {
        int p = acam::link[acam::fail[u]];
        lnk[i + 1] = p;
      }
    }
    auto update = [&](int x, int y = 1) {
      if (x < 1) return ;
      f.update(acam::tin[x], y);
    };
    auto marked = [&](int x) {
      return f.query(acam::tin[x], acam::tout[x]);
    };
    vector<int> add;
    set<int> key;
    int L = str.length() + 1;
    for (int i = str.length(); i >= 1; i--) {
      int x = lnk[i];
      if (x == -1) continue;
      int match = acam::val[x];
      if (i - allS[match].length() < L) {
        L = i - allS[match].length();
        key.insert(x);
        x = acam::fail[x];
      }
      if (x != -1) add.push_back(x);
    }
    for (int x: add) update(x, 1);
    for (int x: key) ans += marked(x) == 0;
    for (int x: add) update(x, -1);
    assert(!marked(1));
  }
  printf("%d\n", ans);
  return 0;
}
```

---
mathjax: true
title: Codeforces Round 620 题解
tags:
  - Codeforces
  - DP
  - Tree
categories:
  - Codeforces
date: 2020-02-16 01:33:00
---

# A Two Rabbits

# B Longest Palindrome

给你 $n$ 个长度为 $m$ 的串，你挑一些出来构造一个最长的回文串。

注意到是每个串长度相同，所以一定是两两配对，加上中间可能会有一个回文串。

# C Air Conditioner

你有一个权值，你每天可以选择把他 $+1,-1$ 或者不变，有 $n$ 个条件，第 $t$ 天，权值范围在 $[l_i,r_i]$ 中。

维护一下你的权值的范围即可。

# D Shortest and Longest LIS

你有长度为 $n-1$ 的 $<$ 和 $>$ 组成的串，表示 $a_i$ 和 $a_{i+1}$ 的大小关系，你现在需要构造出 LIS 最大和最小两个排列。

我的构造方式过于复杂，以致于没来得及交上去（呲牙）。

一种简单的方式，以 LIS 最小为例，你肯定希望整体是在往上走的，因此对于小于号，权值 $+\infty$，大于号权值 $-1$，这样你每次都是往上升的状态，最后每个值必定不同，离散化即可。LIS 最大，考虑整体往下即可。

# E 1-Trees and Queries

你有一棵无根树，$q$ 次询问，独立连一条 $x$ 到 $y$ 的边，询问 $a$ 到 $b$ 是否存在一条长度为 $k$ 的路径，路径可以随意走，可以走重复的点或边。

有 $3$ 种路径，$a \to b$，$a \to x \to y \to b$ 和 $a \to y \to x \to b$，只需要满足长度不超过 $k$，且模 $2$ 同余即可。

# F Animal Observation

给你一个 $n \times m$ 的网格，每行最多选一个长度为 $k$，高度为 $2$ 的矩形（往上延伸一格）（特别地，最后一行可以只选一行），求最大的选中权值和。

加一个空行，简化一下变成 $2$ 到 $n+1$ 行。记 $dp(i,j)$ 表示在第 $i$ 行选择 $j$ 开始的那个矩形的最大权值。

转移分成 $3$ 个部分，首先在所有与左下角是 $(i,j)$ 的矩形没有交的的位置的 $dp$ 值，取一个 $\max$。

剩下两个部分是左边相交和右边相交，两种类似，下面讨论右边相交的情况。($Rect(i,j)$ 为这个位置的矩形权值)

$$
dp(i,j) = Rect(i,j) + \max_{i \le j < i + k}(dp(i-1,j) - \sum_{k=j}^{i+k-1} a_{i-1,k})
$$

将上述转移用前缀和差分后，实际上就是一个滑动窗口取 $\max$ 的问题，单调优化即可。

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
const int maxn = 100000 + 5;

int x, y, a, b;

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d%d%d%d", &x, &y, &a, &b);
    if ((y - x) % (a + b) == 0) {
      printf("%d\n", (y - x) / (a + b));
    } else {
      puts("-1");
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
const int maxn = 100000 + 5;

int n, m, vis[maxn];
string s[maxn];

int main() {
  ios::sync_with_stdio(false); cin.tie(0);
  cin >> n >> m;
  for (int i = 0; i < n; i++) {
    cin >> s[i];
  }
  string ans, mid;
  vector<string> ps;
  map<string,int> mp;
  mp[s[0]] = 0;
  for (int i = 1; i < n; i++) {
    string re(s[i]); reverse(begin(re), end(re));
    if (mp.count(re)) {
      vis[mp[re]] = true;
      vis[i] = true;
      ps.push_back(re);
      mp.erase(re);
    } else {
      mp[s[i]] = i;
    }
  }
  for (int i = 0; i < n; i++) {
    if (vis[i]) continue;
    string re(s[i]); reverse(begin(re), end(re));
    if (re == s[i]) {
      mid = s[i];
    }
  }
  for (auto x: ps) ans += x;
  ans += mid;
  reverse(begin(ps), end(ps));
  for (auto x: ps) {
    reverse(begin(x), end(x));
    ans += x;
  }
  cout << ans.size() << '\n' << ans << '\n';
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
const int maxn = 100000 + 5;

int n, m, t[maxn], l[maxn], r[maxn];

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d%d", &n, &m);
    ll ml = m, mr = m, last = 0;
    bool flag = true;
    for (int i = 1; i <= n; i++) {
      scanf("%d%d%d", t + i, l + i, r + i);
    }
    for (int i = 1; i <= n && flag; i++) {
      mr += t[i] - last;
      ml -= t[i] - last;
      if (ml <= l[i]) {
        if (mr >= l[i]) {
          ml = l[i]; mr = min(mr, 1ll * r[i]);
        } else {
          flag = false;
        }
      } else if (ml <= r[i]) {
        mr = min(mr, 1ll * r[i]);
      } else {
        flag = false;
      }
      last = t[i];
    }
    puts(flag ? "YES" : "NO");
  }
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
const int maxn = 200000 + 5;

int n, ans[maxn], suf[maxn];
char s[maxn];

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d%s", &n, s + 1);
    vector<pair<char,int> > v;
    for (int i = 1, j = 1; i < n; i = j) {
      while (j < n && s[i] == s[j]) j++;
      v.emplace_back(s[i], j - i + (i == 1));
    }

    set<int> st;
    for (int i = 1; i <= n; i++) st.insert(i);

    int pos = 1;
    if (s[1] == '<') {
      auto x = v[0];
      for (int i = pos + x.second - 1; i >= pos; i--) {
        auto it = st.end(); it--;
        ans[i] = *it; st.erase(it);
      }
      pos += x.second;
    }
    for (int tot = s[1] == '<'; tot + 1 < (int)v.size(); tot += 2) {
      int k = v[tot].second + v[tot + 1].second;
      while (k--) {
        auto it = st.end(); it--;
        if (k + 1 > v[tot].second) {
          ans[pos + k] = *it;
        } else {
          ans[pos + v[tot].second - k - 1] = *it;
        }
        st.erase(it);
      }
      pos += v[tot].second + v[tot + 1].second;
    }
    if (v.back().first == '<') {
      for (int i = pos; i <= n; i++) {
        ans[i] = *st.begin(); st.erase(st.begin());
      }
    } else {
      for (int i = pos; i <= n; i++) {
        auto it = st.end(); it--;
        ans[i] = *it; st.erase(it);
      }
    }
    for (int i = 1; i <= n; i++) {
      printf("%d ", ans[i]);
    }
    puts("");

    assert(st.empty());
    for (int i = 1; i <= n; i++) st.insert(i);
    pos = 1;
    if (s[1] == '>') {
      auto x = v[0];
      for (int i = pos + x.second - 1; i >= pos; i--) {
        auto it = st.begin();
        ans[i] = *it; st.erase(it);
      }
      pos += x.second;
    }
    for (int tot = s[1] == '>'; tot + 1 < (int)v.size(); tot += 2) {
      int k = v[tot].second + v[tot + 1].second;
      while (k--) {
        auto it = st.begin();
        if (k + 1 > v[tot].second) {
          ans[pos + k] = *it;
        } else {
          ans[pos + v[tot].second - k - 1] = *it;
        }
        st.erase(it);
      }
      pos += v[tot].second + v[tot + 1].second;
    }
    if (v.back().first == '<') {
      for (int i = pos; i <= n; i++) {
        ans[i] = *st.begin(); st.erase(st.begin());
      }
    } else {
      for (int i = pos; i <= n; i++) {
        auto it = st.end(); it--;
        ans[i] = *it; st.erase(it);
      }
    }
    for (int i = 1; i <= n; i++) {
      printf("%d ", ans[i]);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, q;
vector<int> edge[maxn];

namespace hld {
  int siz[maxn], dep[maxn], fa[maxn], son[maxn], top[maxn];
  void dfs(int u, int f) {
    dep[u] = dep[f] + 1; fa[u] = f; siz[u] = 1; son[u] = 0;
    int m = -1;
    for (auto& v: edge[u]) {
      if (v == f) continue;
      dfs(v, u);
      siz[u] += siz[v];
      if (siz[v] > m) son[u] = v, m = siz[v];
    }
  }
  void dfs(int u, int f, int tp) {
    top[u] = tp;
    if (!son[u]) return;
    dfs(son[u], u, tp); // !
    for (auto& v: edge[u]) {
      if (v == f || v == son[u]) continue; // !
      dfs(v, u, v);
    }
  }
  void build() {
    dfs(1, 0); dfs(1, 0, 1);
  }
  int qlca(int u, int v) {
    while (top[u] != top[v]){
      if (dep[top[u]] < dep[top[v]]) swap(u, v);
      u = fa[top[u]];
    }
    return dep[u] < dep[v] ? u : v;
  }
  int qdis(int u, int v) {
    return dep[u] + dep[v] - 2 * dep[qlca(u, v)];
  }
}
using hld::qdis;

int main() {
  scanf("%d", &n);
  for (int i = 2, u, v; i <= n; i++) {
    scanf("%d%d", &u, &v);
    edge[u].push_back(v);
    edge[v].push_back(u);
  }
  hld::build();
  scanf("%d", &q);
  while (q--) {
    int x, y, a, b, k;
    scanf("%d%d%d%d%d", &x, &y, &a, &b, &k);
    auto check = [&](int d) {
      return d <= k && (k - d) % 2 == 0;
    };
    bool flag = check(qdis(a, b));
    if (!flag) flag = check(qdis(a, x) + qdis(y, b) + 1);
    if (!flag) flag = check(qdis(a, y) + qdis(x, b) + 1);
    puts(flag ? "YES" : "NO");
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
#include <deque>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 20000 + 5;

int n, m, k, a[52][maxn];
ll pmx[maxn], smx[maxn], pre[52][maxn], dp[52][maxn];

ll qsum(int i, int l) {
  return pre[i][l + k - 1] - pre[i][l - 1];
}

int main() {
  scanf("%d%d%d", &n, &m, &k);
  for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= m; j++) {
      scanf("%d", &a[i][j]);
      pre[i][j] = a[i][j] + pre[i][j - 1];
    }
  }
  for (int t = 2; t <= n + 1; t++) {
    ll mxd = 0;
    for (int i = 1; i + k <= m + 1; i++) {
      mxd = max(mxd, dp[t - 2][i]);
      pmx[i] = max(pmx[i - 1], dp[t - 1][i]);
    }
    for (int i = m - k + 1; i >= 1; i--) {
      smx[i] = max(smx[i + 1], dp[t - 1][i]);
    }
    for (int i = 1; i + k <= m + 1; i++) {
      ll tot = mxd;
      if (i > k) tot = max(tot, pmx[i - k]);
      if (i + k <= m - k + 1) tot = max(tot, smx[i + k]);
      dp[t][i] = tot;
    }
    deque<int> dq;
    function<ll(int)> cal = [&](int i) -> ll {
      return dp[t - 1][i] + pre[t - 1][i - 1];
    };
    for (int i = m - k + 1; i >= 1; i--) {
      while (!dq.empty() && dq.front() - i >= k) dq.pop_front();
      while (!dq.empty() && cal(dq.back()) <= cal(i)) dq.pop_back();
      dq.push_back(i);
      dp[t][i] = max(dp[t][i], cal(dq.front()) - pre[t - 1][i + k - 1]);
    }
    while (!dq.empty()) dq.pop_back();
    cal = [&](int i) -> ll {
      return dp[t - 1][i] - pre[t - 1][i + k - 1];
    };
    for (int i = 1; i + k <= m + 1; i++) {
      while (!dq.empty() && i - dq.front() >= k) dq.pop_front();
      while (!dq.empty() && cal(dq.back()) <= cal(i)) dq.pop_back();
      dq.push_back(i);
      dp[t][i] = max(dp[t][i], cal(dq.front()) + pre[t - 1][i - 1]);
    }
    for (int i = 1; i + k <= m + 1; i++) {
      dp[t][i] += qsum(t, i) + qsum(t - 1, i);
    }
  }
  ll ans = 0;
  for (int i = 2; i <= n + 1; i++) {
    for (int j = 1; j + k <= m + 1; j++) {
      ans = max(ans, dp[i][j]);
    }
  }
  cout << ans << endl;
  return 0;
}
```

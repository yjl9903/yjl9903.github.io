---
mathjax: true
title: Educational Round 78 题解
tags:
  - Codeforces
  - Solution
  - Brute Force
  - Tree
  - Construct
  - Math
  - Combinatorial
  - Probability
categories:
  - Codeforces
  - Educational Round
date: 2019-12-20 16:15:50
---

# A Shuffle Hashing

给定一个串 $s$，随机打乱顺序后，询问是否可能是 $t$ 的子串。

暴力枚举 $t$ 的每个子串。
 
# B A and B

智商没对上 T^T。

给定两个整数 $a,b$，第 $i$ 轮选一个数加 $i$，最小多少轮两数相同。

相当于将 $1,2,\dots,n$ 分配到两个数组中，一个和为 $s$ 另一个和为 $t$，且 $s+t=n(n+1)/2,s-t=|a-b|$。

# C Berry Jam

# D Segment Tree

给定 $n$ 条线段，两条线段之间连边当且仅当相交不包含，端点为 $1$ 到 $2n$ 的排列。

扫描线，枚举所有与给线段相交不包含的部分，并查集合并，如果出现环则停止。最多暴力合并 $n$ 次。

最后检查一下并查集完全联通。

# E Tests for problem D

给定一张图，生成一下 D 题的线段。

递归地进行构造，对于子树 $u$，他的左端点为 $l_u$，右端点位置是 $r_u+deg_u+1$（$l_u,r_u$ 为递归参数）。

对于他的所有儿子结点，都与 $u$ 线段相交，并且兄弟结点依次包含，左端点会从右边 $r_u+deg_u$ 一直放到 $r_u+1$。

考虑右端点的限制，因为需要包含前面的兄弟的子树内结点，因此右端点需要往右偏移一段，即上一个兄弟的子树大小的两倍减一（减一由于根节点的左端点在 $u$ 的内部，$u$ 外部的点数为两倍减一）。

初始时，$l_1=r_1=1$。

# F Cards

随机变量 $X$ 服从 $B(n,p)$，求 $E(X^k)$。

根据斯特林数展开

$$
E[X^k]=\sum_{i=0}^k S(k,i) E[X(X-1)\dots (X-i+1)]
$$

伯努利分布的 $i$ 次下降幂

$$
E[X(X-1)\dots (X-i+1)]=A(n,i)p^i
$$

带入得到答案。

参考资料：[Factorial_moment](https://en.wikipedia.org/wiki/Factorial_moment#Binomial_distribution) 和 [What is the kth factorial moment of the binomial distribution?](https://math.stackexchange.com/questions/524050/what-is-the-kth-factorial-moment-of-the-binomial-distribution)。

考虑直接推式子，实际上等价于上面下降幂的推导过程
$$
E(X^k)=\sum_{i=0}^n  {n \choose i} \frac{1}{m}^i(1-\frac{1}{m})^{n-i} i^k  \\
=\sum_{j=0}^{k} S(k,j) \sum_{i=0}^n {n \choose i} \frac{1}{m}^i(1-\frac{1}{m})^{n-i} A(i,j)
$$

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
const int maxn = 100000 + 5;

int main() {
  int T; cin >> T;
  while (T--) {
    string s, t;
    cin >> s >> t;
    vector<int> cnt(26);
    for (auto& x: s) cnt[x - 'a']++;
    int flag = 0;
    for (int i = 0; i < (int)t.length(); i++) {
      vector<int> sum(26);
      for (int j = 0; j < (int)s.length() && i + j < (int)t.length(); j++) {
        sum[t[i + j] - 'a']++;
      }
      if (sum == cnt) {
        flag = 1; break;
      }
    }
    if (flag) puts("YES");
    else puts("NO");
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
const int maxn = 100000 + 5;



int main() {
  int T; cin >> T;
  while (T--) {
    ll a, b; cin >> a >> b;
    if (a == b) {
      puts("0"); continue;
    }
    int d = abs(a - b), tot = 1, sum = 0;
    while (true) {
      sum += tot;
      if (sum >= d && (sum - d) % 2 == 0) {
        break;
      }
      tot++;
    }
    printf("%d\n", tot);
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

int n, a[maxn], pre[maxn];

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d", &n);
    int s1 = 0, s2 = 0;
    for (int i = 1; i <= n + n; i++) {
      scanf("%d", a + i);
      pre[i] = 0;
      if (a[i] == 1) s1++;
      else if (a[i] == 2) s2++;
    }
    map<int,int> mp;
    mp[0] = 0;
    for (int i = n; i >= 1; i--) {
      if (i < n) pre[i] = pre[i + 1];
      if (a[i] == 1) pre[i]++;
      else pre[i]--;
      if (!mp.count(-pre[i])) mp[-pre[i]] = n - i + 1;
    }
    int ans = 2 * n, last = 0;
    if (mp.count(s2 - s1)) ans = mp[s2 - s1];
    for (int i = n + 1; i <= n + n; i++) {
      if (a[i] == 1) last++;
      else last--;
      // dbg(i, last, last + s2 - s1);
      if (mp.count(last + s2 - s1)) {
        // dbg(i, mp[last + s2 - s1]);
        ans = min(ans, mp[last + s2 - s1] + i - n);
      }
    }
    printf("%d\n", ans);
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
const int maxn = 1000000 + 5;

int n, le[maxn], ri[maxn], pre[maxn], id[maxn];
PII a[maxn];

struct BIT {
  int b[maxn];
  void update(int i, int x) {
    for (; i < maxn; i += i & -i) b[i] += x;
  }
  int query(int i) {
    int r = 0;
    for (; i; i -= i & -i) r += b[i];
    return r;
  }
} f, g;

int find(int x) {
  return x == pre[x] ? x : pre[x] = find(pre[x]);
}
int join(int x, int y) {
  x = find(x); y = find(y);
  if (x == y) return 0;
  pre[x] = y;
  return 1;
}

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n + n; i++) pre[i] = i;
  vector<PII> evs;
  for (int i = 1; i <= n; i++) {
    scanf("%d%d", &a[i].first, &a[i].second);
    evs.push_back({a[i].first, 1});
    evs.push_back({a[i].second, -1});
    le[a[i].second] = a[i].first;
    ri[a[i].first] = a[i].second;
    id[a[i].first] = i;
    id[a[i].second] = i;
  }
  sort(begin(evs), end(evs));
  ll tot = 0; int flag = 1;
  set<int> sl, sr;
  for (auto& e: evs) {
    if (e.second == 1) {
      sl.insert(e.first);
      sr.insert(ri[e.first]);
    } else if (e.second == -1) {
      auto it = sl.rbegin();
      vector<int> del;
      while (flag && it != sl.rend()) {
        if (*it <= le[e.first]) break;
        flag &= join(id[e.first], id[*it]);
        del.push_back(*it);
        it++;
      }
      sl.erase(le[e.first]);
      // for (auto& x: del) sl.erase(x);
    }
    if (!flag) break;
  }
  int c = 0;
  for (int i = 1; i <= n; i++) if (find(i) == i) c++;
  if (c > 1) flag = 0;
  if (flag) puts("YES");
  else puts("NO");
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
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 1000000 + 5;

int n, le[maxn], ri[maxn];
vector<int> edge[maxn];

int tot, siz[maxn];
void getsz(int u, int f) {
  siz[u] = 1;
  for (int v: edge[u]) {
    if (v == f) continue;
    getsz(v, u);
    siz[u] += siz[v];
  }
}

void dfs(int u, int f, int beg, int sr) {
  int sz = (int)edge[u].size() - (u != 1);
  le[u] = beg;
  ri[u] = sr + sz + 1;
  int tot = ri[u] - 1, sum = 0;
  for (int v: edge[u]) {
    if (v == f) continue;
    dfs(v, u, tot, ri[u] + sum);
    tot--; sum += 2 * siz[v] - 1;
  }
}

int main() {
  scanf("%d", &n);
  for (int i = 2; i <= n; i++) {
    int u, v; scanf("%d%d", &u, &v);
    edge[u].push_back(v);
    edge[v].push_back(u);
  }
  getsz(1, 0);
  dfs(1, 0, 1, 1);
  for (int i = 1; i <= n; i++) printf("%d %d\n", le[i], ri[i]);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 5000 + 5;

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

int n, m, k, S[maxn][maxn];

int main() {
  cin >> n >> m >> k;
  S[0][0] = 1;
  for (int i = 1; i <= k; i++) {
    for (int j = 1; j <= k; j++) {
      S[i][j] = add(S[i - 1][j - 1], mul(S[i - 1][j], j));
    }
  }
  int ans = 0, f = 1, invm = inv(m);
  for (int i = 1; i <= k && i <= n; i++) {
    int tmp = mul(S[k][i], qpow(invm, i));
    f = mul(f, n + 1 - i);
    tmp = mul(tmp, f);
    ans = add(ans, tmp);
  }
  cout << ans;
  return 0;
}
```

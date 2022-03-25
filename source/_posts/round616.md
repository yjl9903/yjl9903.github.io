---
mathjax: true
title: Codeforces Round 616 题解
tags:
  - Codeforces
  - Interaction
  - DSU
categories:
  - Codeforces
date: 2020-02-03 14:32:06
---

# A Mind Control

给定一个序列 $a_1, a_2,\dots, a_n$，有 $n$ 个人排好队按顺序拿数字，每次只能选择序列首尾拿一个。

你是第 $m$ 个人，你很聪明，但是除你以外所有人都是随便拿。你可以至多选择 $k$ 个人，强迫他们拿首部还是尾部，问最坏情况下，你拿到的最大数字。

枚举你强迫多少人拿首部，剩余的人拿尾部，而你就是枚举剩余的人拿多少首部尾部，每种有两个选择，你取 $\max$ 后，对所有情况取 $\min$，最后对于枚举的东西再取 $\max$。

# B Irreducible Anagrams

定义等价表示两个串，每种字母出现次数相同。定义 $t$ 对于 $s$ 的合法分解，$t$ 和 $s$，等价，至少再某些位置切开后，$t$ 和 $s$ 的对应串等价。

给定一个母串，每次询问当中的一个子串，是否可以存在一个串 $t$ 和它等价，但是不可分解。

串长等于 $1$，其本身与自己不可分解。

出现字母种类大于 $2$，存在不可分解。

出现种类数为 $2$，两端相同则都可以分解，否则存在不可分解。

# C Prefix Enlightenment

一排灯，给定初始状态，有 $k$ 个开关，每个开关控制一个灯的子集，且任意 $3$ 个子集不交。对于每个前缀，求将它们同时点亮的最少开关数。

题目条件转化为每个灯最多出现在两个子集内。将开关建图，边为每个灯泡，问题变成你现在需要对每个点染色，表示开关状态。

先假装所有灯都亮，对图上的每个开关染色。具体地，就是看灯泡的状态决定相邻的开关状态。

于是，枚举前 $i$ 个灯，加入的时候，就是将某些点（对应联通块）连接，并加入答案。如果这个灯只出现在一个子集内，就钦定一下这个开关的颜色。

钦定不选，即设置大小为 $\infty$。

# D Coffee Varieties

藏着一个数组，你现在要猜出当中不同数字个数。

维护着一个长度为 $k$ 询问队列，表示最近的 $k$ 次询问。每次询问会告诉你，你询问的位置是否在队列里出现，然后加入队列，如果队列长度大于 $k$，则删除队首元素。

你还有一个操作是清空队列。清空最多 $30000$ 次，询问最多 ${ 3n^2 \over 2k }$ 次。

考虑每种元素只保留第一个，删除每种元素之后的出现。

将序列按照 $k \over 2$ 的大小分块，块内的相同关系容易处理。

在 Easy Version 中，你只需要暴力枚举两个块即可，期望的次数 $\frac{k}{2}\frac{n}{k}(\frac{n}{k}-1)$ 次。

在 Hard Version 中，将块看成一个完全图，枚举所有边时，可以将块放在一起枚举，即将图划分成一堆路径。

具体地，可以枚举块间的距离 $i$，对于块号模 $i$ 同余的块，连在一起询问。下图中，每种颜色代表一次连续的询问。

![询问方式](https://i.loli.net/2020/02/03/jTS1aGCNdiwQp4b.png)

对于每条路径，需要的次数是 $\frac{k}{2}(\text{边数}+1)$。

总的询问数大约是 $\frac{1}{2}\frac{k}{2}\frac{n}{k}(\frac{n}{k}-1) + \frac{k}{2}\text{路径数}$。

路径数大约是 $1 + 2 + \dots + \frac{n}{k}$，因此询问次数小于 ${ 3n^2 \over 2k }$ 次。

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

int n, m, k, a[maxn];

int main() {
  int T; scanf("%d", &T);
  while (T--) {
    scanf("%d%d%d", &n, &m, &k);
    for (int i = 1; i <= n; i++) {
      scanf("%d", a + i);
    }
    int ans = 0;
    k = min(k, m - 1);
    for (int i = 0; i <= k; i++) {
      int j = n - (k - i) + 1;
      int rest = m - k;
      int tot = inf;
      for (int l = 1; l <= rest; l++) {
        tot = min(tot, max(a[i + l], a[j - (rest - l) - 1]));
      }
      assert(tot != inf);
      ans = max(ans, tot);
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

int n, q;
char s[maxn];
vector<int> pos[26];

int main() {
  scanf("%s%d", s + 1, &q);
  n = strlen(s + 1);
  for (int i = 1; i <= n; i++) {
    pos[s[i] - 'a'].push_back(i);
  }
  while (q--) {
    int l, r; scanf("%d%d", &l, &r);
    if (l == r) {
      puts("Yes");
      continue;
    }
    int sum = 0;
    for (int c = 0; c < 26; c++) {
      int tot = upper_bound(begin(pos[c]), end(pos[c]), r) - lower_bound(begin(pos[c]), end(pos[c]), l);
      if (tot > 0) {
        sum++;
      }
    }
    if (sum == 1) {
      puts("No");
    } else if (sum > 2) {
      puts("Yes");
    } else {
      if (s[l] != s[r]) {
        puts("Yes");
      } else {
        puts("No");
      }
    }
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

int n, k, a[maxn], pre[maxn], siz[maxn];
vector<int> vis[maxn], edge[maxn];

ll ans, cnt[maxn][2];
int col[maxn];
void dfs(int u) {
  cnt[u][col[u]] = 1;
  for (int x: edge[u]) {
    if (vis[x].size() == 1) continue;
    int i = u ^ vis[x][0] ^ vis[x][1];
    if (col[i] == -1) {
      col[i] = col[u] ^ !a[x];
      dfs(i);
    }
  }
}

int find(int x) {
  return x == pre[x] ? x : pre[x] = find(pre[x]);
}
void add(int u, ll s0, ll s1) {
  ans -= min(cnt[u][0], cnt[u][1]);
  cnt[u][0] = min(1ll * inf, cnt[u][0] + s0);
  cnt[u][1] = min(1ll * inf, cnt[u][1] + s1);
  ans += min(cnt[u][0], cnt[u][1]);
}
bool join(int x, int y) {
  x = find(x); y = find(y);
  if (x == y) return false;
  if (siz[x] > siz[y]) swap(x, y);
  add(y, cnt[x][0], cnt[x][1]);
  add(x, -cnt[x][0], -cnt[x][1]);
  pre[x] = y; siz[y] += siz[x];
  return true;
}

int main() {
  scanf("%d%d", &n, &k);
  for (int i = 1; i <= n; i++) {
    scanf("%1d", a + i);
  }
  for (int i = 1; i <= k; i++) {
    pre[i] = i; siz[i] = 1; col[i] = -1;
    int m, x; scanf("%d", &m);
    while (m--) {
      scanf("%d", &x);
      vis[x].push_back(i);
      edge[i].push_back(x);
    }
  }
  for (int i = 1; i <= k; i++) {
    if (col[i] != -1) continue;
    col[i] = 0; dfs(i);
  }
  for (int i = 1; i <= k; i++) {
    dbg(i, col[i], cnt[i][0], cnt[i][1]);
  }
  for (int i = 1; i <= n; i++) {
    if (vis[i].size() == 1) {
      int x = vis[i][0];
      dbg(x, col[x], a[i]);
      if (a[i] != col[x]) {
        add(find(x), inf, 0);
      } else {
        add(find(x), 0, inf);
      }
    } else if (vis[i].size() == 2) {
      join(vis[i][0], vis[i][1]);
    }
    printf("%I64d\n", ans);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, k, alive[maxn];
int query(int x) {
  cout << "? " << x << endl;
  string s; cin >> s;
  if (s == "Y") {
    alive[x] = false;
    return true;
  } else {
    return false;
  }
}
void reset() {
  cout << "R" << endl;
}

int main() {
  cin >> n >> k;
  fill(alive + 1, alive + 1 + n, true);
  int B = max(k / 2, 1), Bcnt = n / B;
  for (int i = 1; i <= Bcnt; i++) {
    for (int j = 0; j < i && i + j < Bcnt; j++) {
      for (int k = j; k < Bcnt; k += i) {
        for (int p = 1; p <= B; p++) {
          query(k * B + p);
        }
      }
      reset();
    }
  }
  cout << "! " << count(alive + 1, alive + 1 + n, true) << endl;
  return 0;
}
```
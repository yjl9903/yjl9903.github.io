---
mathjax: true
title: Educational Round 66 题解
tags:
  - Codeforces
  - Solution
  - Binary Search
categories:
  - Codeforces
  - Educational Round
date: 2019-06-06 22:55:53
---

# A From Hero to Zero

# B Catch Overflow!

# C Electrification

给定 $x$ 正半轴的 $n$ 个点，求一个点 $x$ 到所有点距离的第 $k+1$ 大的最小值。

二分答案，当前值为 $m$，考虑线段 $[a_i-m,a_i+m]$，就是判是否有一个点被覆盖了 $k+1$ 次。

判断过程可以双指针扫一下，具体参考代码。

# D Array Splitting

给定一个序列 $a$，将一个区间分成 $k$ 个连续段，记 $f(i)$ 表示 $i$ 属于第几个段，最大化 $\sum_{i=1}^n a_if(i)$。

考虑贡献，首先每个位置都加了一次，某些后缀多加一些次。

其实就是对所有后缀排序，取前 $k$ 大，注意整体是必选的即可。

# E Minimal Segment Cover

给定 $n$ 条线段，询问 $q$ 次一个线段内所有点是否被覆盖，包含非整数点。

因为要包含整数之间的部分，因此考虑一个贪心的跳转的过程，每个点跳一次到最右端点，然后从这个位置继续往后跳。

这个跳转过程可以用倍增进行加速，预处理每个位置被一条线段覆盖到的最右端点，倍增一下。

# F The Number of Subpermutations

给定序列 $a_1, a_2, \dots, a_n$，求有多少个子区间是对应长度的排列。

观察一下，要么 $1$ 很少，要么重复的数字很多。枚举包含 $1$ 的子区间，向右跑到出现相同数字停止，判断这个点为右端点的子区间是否是一个排列，即判断两个集合是否相同。随机一个权值异或判断即可。

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



int main() {
    int T; scanf("%d", &T);
    while (T--) {
        ll n, k; 
        cin >> n >> k;
        ll ans = 0;
        while (n) {
            ans += n % k;
            n -= n % k;
            if (n) ans++;
            n /= k;
        }
        cout << ans << '\n';
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
const ll inf = 4294967295;
const int maxn = 100000 + 5;

int n;
ll x;
char s[20];

int main() {
    scanf("%d", &n);
    ll tot = 1;
    vector<ll> v; v.push_back(1);
    for (int i = 1, a; i <= n; i++) {
        scanf("%s", s);
        if (s[0] == 'f') {
            scanf("%d", &a);
            if (v.back() == -1) v.push_back(-1);
            else if (1ll * v.back() * a > inf) v.push_back(-1);
            else v.push_back(1ll * v.back() * a);
            tot *= 1ll * a;
        } else if (s[0] == 'a') {
            if (v.back() == -1) return puts("OVERFLOW!!!"), 0;
            x += v.back();
            if (x > inf) return puts("OVERFLOW!!!"), 0;
        } else if (s[0] == 'e') {
            v.pop_back();
        }
    }
    cout << x << endl;
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
const int maxn = 200000 + 5;

int n, k, a[maxn];
PII b[maxn]; int tot;

int check(int x) {
    int l = 1, r = 1, c = 0;
    while (r <= n) {
        if (a[r] - x <= a[l] + x) {
            r++; c++;
            if (c == k + 1) {
                tot = a[l] + x;
                return 1;
            }
        } else {
            l++; c--;
        }
    }
    return 0;
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &k);
        for (int i = 1; i <= n; i++) scanf("%d", a + i);
        if (k == 0) {
            printf("%d\n", a[1]); continue;
        }
        int l = 1, r = 1e9, ans = 0;
        while (l <= r) {
            int m = (l + r) / 2;
            if (check(m)) r = m - 1, ans = tot;
            else l = m + 1;
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
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

int n, a[maxn], k;
ll suf[maxn];

int dp[maxn][20];
void init() {
    for (int i = 1; i <= n; i++) dp[i][0] = i;
    for (int j = 1; j < 20; j++)
        for (int i = 1; i + (1 << j) <= n + 1; i++) {
            if (suf[dp[i][j - 1]] < suf[dp[i + (1 << (j - 1))][j - 1]])
                dp[i][j] = dp[i + (1 << (j - 1))][j - 1];
            else 
                dp[i][j] = dp[i][j - 1];
        }
}
int rmq(int l, int r) {
    int k = 0; while ((1 << (k + 1)) <= r - l + 1) k++;
    return max(dp[l][k], dp[r - (1 << k) + 1][k]);
}

int main() {
    scanf("%d%d", &n, &k);
    ll ans = 0;
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = n; i >= 1; i--) suf[i] = a[i] + suf[i + 1];
    ans = suf[1];
    vector<ll> v;
    for (int i = 2; i <= n; i++) v.push_back(suf[i]);
    sort(v.begin(), v.end(), [](ll a, ll b) { return a > b; });
    for (int i = 0; i < k - 1; i++) ans += v[i];
    cout << ans;
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 200000 + 5;
const int maxl = 500000 + 5;

int n, q, l[maxn], r[maxn], nxt[maxl][20];
vector<int> seg[maxl];

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= n; i++) {
        scanf("%d%d", l + i, r + i);
        seg[l[i]].push_back(r[i]);
    }
    int mx = -1;
    for (int i = 0; i < maxl; i++) {
        for (int& x: seg[i]) mx = max(mx, x);
        if (mx >= i) nxt[i][0] = mx;
        else nxt[i][0] = i;
    }
    for (int j = 1; j < 20; j++) {
        for (int i = 0; i < maxl; i++) {
            nxt[i][j] = nxt[nxt[i][j - 1]][j - 1];
        }
    }
    while (q--) {
        int x, y; scanf("%d%d", &x, &y);
        int now = x, ans = 0;
        if (nxt[x][19] < y) {
            puts("-1"); continue;
        }
        for (int i = 19; i >= 0; i--) {
            if (nxt[now][i] < y) {
                ans += 1 << i;
                now = nxt[now][i];
            }
        }
        if (now < y) ans++;
        printf("%d\n", ans);
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
#include <random>
#include <ctime>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
using ll = long long;
using PII = pair<int,int>;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

mt19937_64 rnd(time(nullptr));

int n, a[maxn];
ll val[maxn], pre[maxn], b[maxn];

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
    val[i] = rnd();
    pre[i] = val[i] ^ pre[i - 1];
  }
  ll ans = 0;
  for (int tp = 0; tp < 2; tp++) {
    for (int i = 1; i <= n; i++) {
      b[i] = val[a[i]] ^ b[i - 1];
    }
    for (int i = 1; i <= n; i++) {
      if (a[i] > 1) continue;
      set<int> vis = { 1 };
      ans += tp == 0;
      for (int j = i + 1, mx = 1; j <= n; j++) {
        if (vis.count(a[j])) break;
        mx = max(mx, a[j]); vis.insert(a[j]);
        if (j >= mx && pre[mx] == (b[j] ^ b[j - mx])) {
          ans++;
        }
      }
    }
    reverse(a + 1, a + 1 + n);
  }
  cout << ans << endl;
  return 0;
}
```

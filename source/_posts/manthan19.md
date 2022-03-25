---
mathjax: true
title: Codeforces Manthan19 题解
tags:
  - Codeforces
  - Solution
  - Fenwick Tree
  - Binary Search
  - RMQ
categories:
  - Codeforces
date: 2019-08-26 01:46:04
---

回到梦（题解）开始的地方？

# A XORinacci

# B Uniqueness

# C Magic Grid

用 $[0,n^2-1]$ 填一个 $n \times n$ 的矩阵，使得每行每列异或和相同，其中 $n$ 是 $4$ 的倍数。

不知道为啥，反正观察出四个填一个 $2 \times 2$ 就行了。

# D Restore Permutation

有一个排列 $p$，现在你知道对于位置 $i$，排列上前 $i-1$ 个数小于 $p_i$ 的和为 $s_i$，要求恢复出 $p_i$。

从后往前，在树状数组上二分即可。

# E Let Them Slide

最大长度为 $w$，给定 $n$ 个滑动窗口，每个窗口有 $l_i$ 个数，回答 $w$ 个独立询问，所有窗口任意滑动，第 $i$ 列的最大权值和。

差分答案。

枚举每一行，枚举整个值域范围，对于位置 $i$，他可能是从窗口中某一个范围转移过来，注意到，如果窗口很短，那么中间会有很多位置只由窗口最大值贡献而来，那么我们实际上只需要枚举值域范围某个前缀和后缀，中间一部分可以在差分数组上打标记。如果窗口很长，即长度大于 $w \over 2$，那么这样的窗口最多出现 $2$ 个，因此总复杂度为 $O(\sum l_i)$。

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

int n, a, b;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d%d", &a, &b, &n);
        // 0 -> a, 1 -> b, 2 -> a ^ b, 3 -> a, 4 -> b
        if (n % 3 == 0) printf("%d\n", a);
        else if (n % 3 == 1) printf("%d\n", b);
        else printf("%d\n", a ^ b);
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
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, a[maxn];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    int ans = inf;
    for (int l = 0; l <= n; l++) {
        set<int> st;
        int flag = 0;
        for (int i = 1; i <= l; i++) {
            if (st.count(a[i])) {
                flag = 1; break;
            }
            st.insert(a[i]);
        }
        if (flag) break;
        ans = min(ans, n - l);
        for (int r = n; r > l; r--) {
            if (st.count(a[r])) break;
            st.insert(a[r]);
            ans = min(ans, r - l - 1);
        }
    }
    printf("%d\n", ans);
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
const int maxn = 2000 + 5;

int n, a[maxn][maxn];

int main() {
    cin >> n;
    int tot = 0;
    for (int i = 1; i <= n; i += 2) {
        for (int j = 1; j <= n; j += 2) {
            a[i][j] = tot;
            a[i][j + 1] = tot + 1;
            a[i + 1][j] = tot + 2;
            a[i + 1][j + 1] = tot + 3;
            tot += 4;
        }
    }
    for (int i = 1; i <= n; i++) {
        int sum1 = 0, sum2 = 0;
        for (int j = 1; j <= n; j++) {
            sum1 ^= a[i][j];
            sum2 ^= a[j][i];
        }
        dbg(i, sum1, sum2);
    }
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            printf("%d ", a[i][j]);
        }
        puts("");
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
#include <cassert>
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

int n, p[maxn];
ll s[maxn];

struct BIT {
    ll a[maxn];
    inline int lowbit(int x) { return x & -x; }
    void insert(int i, int x) {
        for (; i <= n; i += lowbit(i)) a[i] += x;
    }
    ll query(int i) {
        ll r = 0;
        for (; i; i -= lowbit(i)) r += a[i];
        return r;
    }
} f;

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%I64d", s + i);
    for (int i = 1; i <= n; i++) f.insert(i, i);
    for (int i = n; i >= 1; i--) {
        int l = 0, r = n, ans = -1;
        while (l <= r) {
            int m = (l + r) / 2;
            if (f.query(m) <= s[i]) ans = m + 1, l = m + 1;
            else r = m - 1;
        }
        dbg(ans);
        assert(ans != -1);
        f.insert(ans, -ans);
        p[i] = ans;
        // int x = f.findx(s[i] + 1);
        // p[i] = x;
        // f.insert(x, -1);
    }
    for (int i = 1; i <= n; i++) printf("%d ", p[i]);
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
#include <set>
#include <utility>
#include <functional>
#include <cassert>
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

int n, w;
ll ans[maxn];

int dp[21][maxn];
void solve(const vector<int>& a) {
    int n = (int)a.size();
    if (n == w) {
        for (int i = 0; i < w; i++) {
            ans[i] += a[i];
            ans[i + 1] -= a[i];
        }
        return ;
    }
    for (int i = 0; i < n; i++) dp[0][i] = i;
    for (int j = 1; j <= 20; j++) {
        for (int i = 0; i + (1 << j) <= n; i++) {
            int l = dp[j - 1][i];
            int r = dp[j - 1][i + (1 << (j - 1))];
            if (a[l] > a[r]) dp[j][i] = l;
            else dp[j][i] = r;
        }
    }
    auto qmax = [&](int l, int r) -> int {
        int k = __lg(r - l + 1);
        int x = dp[k][l], y = dp[k][r - (1 << k) + 1];
        if (a[x] > a[y]) return x;
        else return y;
    };
    for (int i = 0; i < w; i++) {
        int l = max(i - w + n, 0);
        int r = min(i, n - 1);
        assert(l <= r);
        int mx = a[qmax(l, r)];
        if (mx < 0 && (i >= n || i < w - n)) {
            mx = 0;
        }
        ans[i] += mx;
        if (l == 0 && r == n - 1 && i >= n && w >= 2 * n + 1) {
            i = max(i, w - 1 - n);
        }
        ans[i + 1] -= mx;
    }
}

int main() {
    scanf("%d%d", &n, &w);
    for (int i = 1, x, m; i <= n; i++) {
        scanf("%d", &m);
        vector<int> line;
        for (int j = 1; j <= m; j++) {
            scanf("%d", &x);
            line.push_back(x);
        }
        solve(line);
    }
    for (int i = 0; i < w; i++) printf("%I64d ", ans[i] += ans[i - 1]);
    return 0;
}
```
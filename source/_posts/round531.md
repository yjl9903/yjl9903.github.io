---
mathjax: true
title: Codeforces Round 531 题解
tags:
  - Codeforces
  - Solution
categories:
  - Codeforces
date: 2019-01-10 11:26:10
---

每道题都 WA 了一发，思路不清，代码还长是怎么回事？

# A Integer Sequence Dividing

把 $1$ 到 $n$ 的所有整数划分进两个集合 $S_1,S_2$，最小化 $|sum(S_1)-sum(S_2)|$，模 $4$ 讨论即可。

# B Array K-Coloring

一共有 $k$ 种颜色，对长度为 $n$ 的数组每个位置染色，要求数字相同的位置颜色不同，并且每种颜色都要出现过。

每种数字维护一个最后出现的颜色，一开始预处理好每种颜色的开始位置，防止颜色出现没有 $k$ 种。

# C Doors Breaking and Repairing

两个人修门和拆门，一共 $n$ 个门，轮流选择一个门拆和修，如果拆到 $0$ 后，就不能再修了，两个人都很机智，求最多拆多少个门。

如果 $x > y$，肯定可以把全部都拆掉。

如果 $x \le y$，自己肯定不会拆大于 $x$，小于 $x$ 轮流拆和修，统计一下即可。

# D Balanced Ternary String

贪心。

# E Monotonic Renumeration

给一个长度为 $n$ 的数组 $A$，对任意数字相同位置，生成数组 $B$ 所有对应位置的值也相同，并且生成数组 $B$ 满足 $B[0]=0$ 并且每个位置都和前面要么相同要么 $+1$，问方案数。

把所有相同位置线段合并，剩余 $cnt$ 条线段，答案为 $2^{cnt-1}$。

# F Elongated Matrix

给一个 $n \times m$ 的矩阵，行可以任意两两交换，问交换后从上到下从左到右遍历这个矩阵，求相邻位置差的绝对值的最大值。

二分答案，将可以相邻的两行连边，判断哈密顿通路。

注意预处理和哈密顿通路的状压。

<!--more-->

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

int n;

int main(){
    cin >> n;
    if (n % 4 == 0) puts("0");
    else if (n % 4 == 1) puts("1");
    else if (n % 4 == 2) puts("1");
    else puts("0");
    return 0;
}
```

## B

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

int n, k, a[maxn], cnt[maxn], last[maxn], ans[maxn];

int main(){
    scanf("%d%d", &n, &k);
    for (int i = 1; i <= n; i++) scanf("%d", a + i), cnt[a[i]]++;
    int s = 1;
    for (int i = 1; i <= 5000; i++) {
        if (cnt[i] > k) return puts("NO");
        last[i] = s;
        s = (s + cnt[i] + k - 1) % k + 1;
    }
    puts("YES");
    int tot = 0; 
    for (int i = 1; i <= n; i++) {
        last[a[i]] = last[a[i]] % k + 1;
        ans[i] = last[a[i]];
    }
    for (int i = 1; i <= n; i++) {
        printf("%d ", ans[i]);
    }
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

int n, x, y, a[maxn];
multiset<int> st;

int main(){
    scanf("%d%d%d", &n, &x, &y);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    if (x > y) return printf("%d", n), 0;
    int ans = 1;
    for (int i = 1; i <= n; i++) if (a[i] <= x) ans++;
    cout << ans / 2;
    // int ans = 0;
    // while (true) {
    //     auto r = st.upper_bound(x);
    //     if (r == st.begin()) break;
    //     r--; ans++;
    //     st.erase(r);
    //     st.erase(*st.begin());
    // }
    // cout << ans;
    return 0;
}
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 300000 + 5;

int n; char s[maxn];

int main(){
    scanf("%d%s", &n, s + 1);
    int sum[3] = {0, 0, 0};
    for (int i = 1; i <= n; i++) sum[s[i] - '0']++;
    int nd = n / 3, pre[3] = {0, 0, 0};
    for (int i = 1; i <= n; i++) {
        if (sum[0] < nd) {
            if (s[i] == '1' && sum[1] > nd) {
                s[i] = '0';
                sum[0]++; sum[1]--;
            } else if (s[i] == '2' && sum[2] > nd) {
                s[i] = '0';
                sum[0]++; sum[2]--;
            }
        } else if (sum[1] < nd) {
            if (s[i] == '0' && sum[0] > nd && pre[0] == nd) {
                s[i] = '1';
                sum[0]--; sum[1]++;
            } else if (s[i] == '2' && sum[2] > nd) {
                s[i] = '1';
                sum[1]++; sum[2]--;
            }
        } else if (sum[2] < nd) {
            if (s[i] == '0' && sum[0] > nd && pre[0] == nd) {
                s[i] = '2';
                sum[0]--; sum[2]++;
            } else if (s[i] == '1' && sum[1] > nd && pre[1] == nd) {
                s[i] = '2';
                sum[1]--; sum[2]++;
            }
        }
        pre[s[i] - '0']++;
    }
    cout << s + 1;
    return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <map>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int mod = 998244353;
const int maxn = 200000 + 5;

ll qpow(ll x, ll n) {
    ll r = 1;
    while (n) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r % mod;
}

int n, a[maxn];
map<int,PII> mp;

int main(){
    scanf("%d", &n); for (int i = 1; i <= n; i++) scanf("%d", a + i);
    int tot = 0;
    for (int i = 1; i <= n; i++) {
        if (mp.count(a[i])) {
            mp[a[i]].second = i;
        } else {
            mp[a[i]].first = mp[a[i]].second = i;
        }
    }
    vector<PII> v;
    for (auto& x: mp) {
        v.push_back({x.second.first, x.second.second});
    }
    sort(v.begin(), v.end());
    int l = v[0].first, r = v[0].second, cnt = v[0].first - 1;
    for (int i = 1; i < v.size(); i++) {
        if (v[i].first <= r) {
            r = max(r, v[i].second);
        } else {
            cnt += v[i].first - r;
            l = v[i].first; 
            r = v[i].second;
        }
    }
    cnt += n - r;
    cout << qpow(2ll, cnt);
    return 0;
}
```

## F

```c++
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 200000 + 5;

int n, m, a[20][10000 + 10], vis[30], lk[30][30], ck[30][30];
vector<int> edge[20];

int s, tag, mask[maxn][20];
int dfs(int u, int cnt, int mk) {
    if (cnt == n) {
        if (ck[s][u] < tag) return 0;
        return 1;
    }
    mk |= (1 << u);
    if (mask[mk][u] == s) return 0;
    for (int& v: edge[u]) {
        if (vis[v]) continue;
        vis[v] = 1;
        if (dfs(v, cnt + 1, mk)) return 1;
        vis[v] = 0;
    }
    mask[mk][u] = s;
    return 0;
}
int check(int x) {
    tag = x;
    for (int i = 1; i <= n; i++) edge[i].clear();
    for (int i = 1; i <= n; i++) {
        for (int j = i + 1; j <= n; j++) {
            if (lk[i][j] >= x) {
                edge[i].push_back(j);
                edge[j].push_back(i);
            }
        }
    }
    ms(vis, 0); ms(mask, 0);
    for (int i = 1; i <= n; i++) {
        s = i; vis[s] = 1;
        if (dfs(s, 1, 0)) return 1;
        vis[s] = 0;
    }
    return 0;
}

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++) scanf("%d", &a[i][j]);
    for (int i = 1; i <= n; i++) for (int j = i + 1; j <= n; j++) {
        int mn = 1e9;
        for (int k = 1; k <= m ; k++) {
            mn = min(mn, abs(a[i][k] - a[j][k]));
        }
        lk[i][j] = mn;
    }
    for (int i = 1; i <= n; i++) for (int j = 1; j <= n; j++) {
        int mn = 1e9;
        for (int k = 2; k <= m; k++) {
            mn = min(mn, abs(a[i][k] - a[j][k - 1]));
        }
        ck[i][j] = mn;
    }
    int l = 0, r = 1e9, ans = 0;
    while (l <= r) {
        int m = l + r >> 1;
        if (check(m)) l = m + 1, ans = m;
        else r = m - 1;
    }
    cout << ans;
    return 0;
}
```
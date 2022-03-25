---
mathjax: true
title: Educational Round 64 题解
tags:
  - Codeforces
  - Solution
  - Tree
  - Greedy
categories:
  - Codeforces
  - Educational Round
date: 2019-05-02 20:12:58
---

# A Inscribed Figures

# B Ugly Pairs

# C Match Points

给定序列 $a$，求最多能匹配多少对差值绝对值大于 $z$，一个位置不能匹配多次。

显然，排序。我们不能对于每个数贪心的选最小的匹配，但是我们注意到答案最大是 $[{ n\over 2}]$，从前后一半选显然最优。

对于前一半，在后一半匹配一个最小的即可。

# D 0-1-Tree

给定一棵带 $01$ 权值的无根树，求多少条形如 $111\dots 111$， $000 \dots 000$ 和 $000\dots000111\dots111$ 的路径。

并查集维护 $1$ 边连接和 $0$ 边连接的点集，枚举每个点作为第三种路径分界的情况，即 $1$ 和 $0$ 的联通块大小减一的乘积。

# E Special Segments of Permutation

给定一个 $1$ 到 $n$ 的排列，求有多少对 $(l,r)$，满足 $a[l]+a[r]=\max_{i=l}^r a[i]$。

考虑枚举最大值位置，左右两边选择一对能够凑成最大值，只需要枚举短的一边，判断配对的另外一个数是否在另外一边。

复杂度证明类似于启发式合并，$O(n\log n)$。

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

int n, a[maxn];

int main() {
    scanf("%d", &n);
    int ans = 0, flag = 0;
    for (int i = 1; i <= n; i++) {
        scanf("%d", a + i);
        if (i > 1) {
            if (a[i] == 1) {
                if (a[i - 1] == 2) ans += 3;
                else if (a[i - 1] == 3) ans += 4;
            } else if (a[i] == 2) {
                if (a[i - 1] == 1) {
                    if (i > 2) {
                        if (a[i - 2] == 3) ans += 2;
                        else ans += 3;
                    } else {
                        ans += 3;
                    }
                } else if (a[i - 1] == 3) flag = 1;
            } else if (a[i] == 3) {
                if (a[i - 1] == 1) ans += 4;
                else if (a[i - 1] == 2) flag = 1;
            }
        }
    }
    if (flag) return puts("Infinite"), 0;
    puts("Finite");
    cout << ans;
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
#include <string>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

char s[maxn];
int cnt[maxn];

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%s", s);
        ms(cnt, 0);
        int n = strlen(s);
        for (int i = 0; i < n; i++) cnt[s[i] - 'a']++;
        string ans;
        for (int i = 0; i < 26; i += 2) {
            int x = cnt[i];
            while (x--) ans += i + 'a';
        }
        for (int i = 1; i < 26; i += 2) {
            int x = cnt[i];
            while (x--) ans += i + 'a';
        }
        int flag = 0;
        for (int i = 1; i < n; i++) if (abs(ans[i] - ans[i - 1]) == 1) {
            flag = 1; break;
        }
        if (flag) {
            ans = "";
            for (int i = 1; i < 26; i += 2) {
                int x = cnt[i];
                while (x--) ans += i + 'a';
            }
            for (int i = 0; i < 26; i += 2) {
                int x = cnt[i];
                while (x--) ans += i + 'a';
            }
            int flag = 0;
            for (int i = 1; i < n; i++) if (abs(ans[i] - ans[i - 1]) == 1) {
                flag = 1; break;
            }
            if (flag) puts("No answer");
            else puts(ans.c_str());
        } else puts(ans.c_str());
    }
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
#include <set>
#include <queue>
#include <functional>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 200000 + 5;

int n, z, a[maxn];
multiset<int> st;

struct node {
    int val, tag;
    bool operator<(const node& b) const {
        return val > b.val;
    }
};

int main() {
    scanf("%d%d", &n, &z);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    sort(a + 1, a + 1 + n);
    int ans = 0;
    for (int i = n / 2 + 1; i <= n; i++) st.insert(a[i]);
    for (int i = 1; i <= n / 2; i++) {
        auto it = st.lower_bound(a[i] + z);
        if (it == st.end()) continue;
        st.erase(it); ans++;
    }
    cout << ans << endl;
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

int n;
vector<PII> edge[maxn];

ll ans;
struct DSU {
    int pre[maxn], siz[maxn];
    int find(int x) {
        return x == pre[x] ? x : pre[x] = find(pre[x]);
    }
    void join(int x, int y) {
        x = find(x); y = find(y);
        if (x == y) return ;
        pre[x] = y; siz[y] += siz[x];
    }
    int check(int x, int y) {
        return find(x) == find(y);
    }
    void init(int n) {
        for (int i = 1; i <= n; i++) pre[i] = i, siz[i] = 1;
    }
    ll cal() {
        ll ans = 0;
        for (int i = 1; i <= n; i++) if (i == pre[i]) {
            ans += 1ll * siz[i] * (siz[i] - 1);
        }
        return ans;
    }
    int size(int u) {
        return siz[find(u)];
    }
} f[2];

set<int> nb[maxn];
int uu[maxn], vv[maxn], ww[maxn];

int main() {
    scanf("%d", &n);
    f[0].init(n); f[1].init(n);
    for (int i = 2, u, v, w; i <= n; i++) {
        scanf("%d%d%d", &u, &v, &w);
        uu[i] = u; vv[i] = v; ww[i] = w;
        f[w].join(u, v);
    }
    ans = f[0].cal() + f[1].cal();
    dbg(ans);
    for (int i = 1; i <= n; i++) {
        if (f[0].size(i) > 1 && f[1].size(i) > 1) {
            ans += 1ll * (f[0].size(i) - 1) * (f[1].size(i) - 1);
            dbg(i, f[0].size(i) - 1, f[1].size(i) - 1);
        }
    }
    cout << ans << endl;
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

int n, a[maxn], pos[maxn];

int dp[maxn][20];
void init() {
    for (int j = 1; j < 20; j++)
        for (int i = 1; i + (1 << j) <= n + 1; i++)
            dp[i][j] = max(dp[i][j - 1], dp[i + (1 << (j - 1))][j - 1]);
}
int qmax(int l, int r) {
    int k = 0; while ((1 << (k + 1)) <= r - l + 1) k++;
    return max(dp[l][k], dp[r - (1 << k) + 1][k]);
}

ll solve(int l, int r) {
    if (r - l <= 1) return 0;
    dbg(l, r);
    int mx = qmax(l, r), mid = pos[mx];
    ll ans = 0;
    if (mid - l < r - mid) {
        for (int i = l; i < mid; i++) if (pos[mx - a[i]] > mid && pos[mx - a[i]] <= r) ans++;
    } else {
        for (int i = mid + 1; i <= r; i++) if (pos[mx - a[i]] < mid && pos[mx - a[i]] >= l) ans++;
    }
    return ans + solve(l, mid - 1) + solve(mid + 1, r);
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i), dp[i][0] = a[i], pos[a[i]] = i;
    init();
    cout << solve(1, n) << endl;
    return 0;
}
```
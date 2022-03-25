---
mathjax: true
title: Codeforces Round 540 题解
tags:
  - Codeforces
  - Solution
  - Binary Search
  - Tree
categories:
  - Codeforces
date: 2019-02-20 17:15:37
---

# A Water Buying

# B Tanya and Candies

给定一个序列，求有多少个位置删除后，奇数位置和等于偶数位置和。

预处理每个后缀的奇数和，偶数和即可。

# C Palindromic Matrix

回文矩阵，左右翻一下，上下翻一下，都和原矩阵相同。

注意一下奇数时的处理即可。

写错了个条件 FST 了 T^T。

# D Coffee and Coursework

有 $n$ 杯咖啡，要写 $m$ 页书。每杯咖啡有权值 $a_i$，每天喝第一杯咖啡可以写 $a_i$ 页，第二杯咖啡 $max(0,a_i-1)$ 页，第三杯咖啡 $max(0,a_i-2)$ 页。求最少多少天可以写完 $m$ 页。

对所有咖啡权值排序。

二分答案。显然，权值大的咖啡尽量在每天第一个喝。

# E Yet Another Ball Problem

# F1 Tree Cutting (Easy Version)

<!--more-->

快乐 $div3$，一度假装自己 ak 了，事实上各种意义上都失败了。

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
const int maxn = 100000 + 5;

ll n, a, b;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%I64d%I64d%I64d", &n, &a, &b);
        if (b > 2 * a) b = 2 * a;
        ll ans = 1ll * n / 2 * b + n % 2 * a;
        printf("%I64d\n", ans);
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
const int maxn = 200000 + 5;

int n, a[maxn], suf[maxn][2];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = n; i >= 1; i--) {
        suf[i][i % 2] = a[i] + suf[i + 1][i % 2];
        suf[i][(i + 1) % 2] = suf[i + 1][(i + 1) % 2];
    }
    int a = 0, b = 0, ans = 0;
    for (int i = 1; i <= n; i++) {
        if (i % 2 == 1) {
            if (a + suf[i + 1][0] == b + suf[i + 1][1]) ans++;
            a += ::a[i];
        } else {
            if (a + suf[i + 1][0] == b + suf[i + 1][1]) ans++;
            b += ::a[i];
        }
    }
    cout << ans << endl;
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
#include <map>
#define assert(x) do{int a=1,b=0;if(!(x))printf("%d",a/b);}while(0)
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
const int maxn = 10000 + 5;

int n, a[maxn], ans[30][30];
map<int,int> mp, mp2, mp3;

int main() {
    scanf("%d", &n);
    for (int i = 0; i < n * n; i++) scanf("%d", a + i), mp[a[i]]++;
    vector<int> v;
    for (auto& x: mp) {
        if (x.second % 4 == 0) continue;
        if (x.second % 4 >= 2) {
            dbg(x.first, x.second);
            mp2[x.first] = 2;
            x.second -= 2;
        }
        if (x.second % 4 == 1) {
            dbg(x.first, x.second);
            v.push_back(x.first);
            x.second--;
        }
    }
    if ((int)v.size() > 1) return puts("NO"), 0;
    if ((int)v.size() == 1) {
        if (n % 2 == 0) return puts("NO"), 0;
        dbg(v.front());
        ans[n / 2 + 1][n / 2 + 1] = v.front();
    }
    if (!mp2.empty()) {
        if (n % 2 == 0) return puts("NO"), 0;
        int x = n / 2 + 1, y = 1, flag = 1;
        int yb = n / 2 + 1;
        dbg((int)mp2.size());
        if ((int)mp2.size() > n / 2 * 2) return puts("NO"), 0;
        for (auto& t: mp2) {
            if (flag) {
                ans[x][y] = ans[x][n + 1 - y] = t.first;
                y++;
                if (y == yb) {
                    flag = 0; x = 1; y = n / 2 + 1;
                }
            } else {
                ans[x][y] = ans[n + 1 - x][y] = t.first;
                x++;
            }
        }
    }
    int x = 1, y = 1, flag = 0;
    for (auto& t: mp) {
        int cnt = t.second;
        if (flag) {
            mp3[t.first] = t.second; continue;
        }
        while (cnt) {
            // dbg(x, y);
            ans[x][y] = ans[n + 1 - x][y] = ans[n + 1 - x][n + 1 - y] = ans[x][n + 1 - y] = t.first;
            y++; if (y == n / 2 + 1) y = 1, x++;
            cnt -= 4;
            if (x == n / 2 + 1) {
                mp3[t.first] = cnt; flag = 1; break;
            }
        }
    }
    if (n % 2 == 1) {
        for (int i = 1; i <= n / 2; i++) if (ans[i][n / 2 + 1] == 0) {
            while (!mp3.empty() && mp3.begin()->second == 0) {
                mp3.erase(mp3.begin());
            }
            ans[i][n / 2 + 1] = ans[n + 1 - i][n / 2 + 1] = mp3.begin()->first;
            mp3.begin()->second -= 2;
        }
        for (int i = 1; i <= n / 2; i++) if (ans[n / 2 + 1][i] == 0) {
            while (!mp3.empty() && mp3.begin()->second == 0) {
                mp3.erase(mp3.begin());
            }
            ans[n / 2 + 1][i] = ans[n / 2 + 1][n + 1 - i] = mp3.begin()->first;
            mp3.begin()->second -= 2;
        }
    }
    puts("YES");
    for (int i = 1; i <= n; i++, puts("")) for (int j = 1; j <= n; j++) printf("%d ", ans[i][j]);
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
#define assert(x) do{int a=1,b=0;if(!(x))printf("%d",a/b);}while(0)
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
const int maxn = 400000 + 5;

int n, m, a[maxn];

int check(int x) {
    ll sum = 0, pd = n / x;
    for (int i = 0, k = 0; i < n; i += x, k++) {
        for (int j = i; j < i + x; j++) sum += max(0, a[j] - k);
    }
    // dbg(sum);
    // for (int i = n / x * x; i < n; i++) {
    //     sum += max(0, a[i] - pd);
    // }
    dbg(x, sum);
    return sum >= m;
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    sort(a, a + n); reverse(a, a + n);
    int ans = -1, l = 1, r = n;
    while (l <= r) {
        int m = (l + r) >> 1;
        if (check(m)) ans = m, r = m - 1;
        else l = m + 1;
    }
    cout << ans << endl;
    // for (int i = 1; i <= n; i++) check(i);
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
const int maxn = 300000 + 5;

int n, k, ans[maxn][2];

int main() {
    cin >> n >> k;
    if (n > 1ll * k * (k - 1)) return puts("NO"), 0;
    int tot = 1, f = 0;
    for (int i = 1; i <= k; i++) {
        for (int j = i + 1; j <= k; j++) {
            ans[tot][f] = i;
            ans[tot][f ^ 1] = j;
            tot++; f ^= 1;
            ans[tot][f] = i;
            ans[tot][f ^ 1] = j;
            tot++; f ^= 1;
            if (tot > n) break;
        }
        if (tot > n) break;
    }
    puts("YES");
    for (int i = 1; i <= n; i++) printf("%d %d\n", ans[i][0], ans[i][1]);
    return 0;
}
```

## F1

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
const int maxn = 300000 + 5;

vector<int> edge[maxn];
int n, ans, a[maxn], cnt[3], siz[maxn][3];

void dfs(int u, int f) {
    siz[u][a[u]]++;
    for (int& v: edge[u]) {
        if (v == f) continue;
        dfs(v, u);
        if (siz[v][1] == 0 || siz[v][2] == 0) {
            if (cnt[1] - siz[v][1] == 0 || cnt[2] - siz[v][2] == 0) {
                ans++;
            }
        }
        siz[u][0] += siz[v][0];
        siz[u][1] += siz[v][1];
        siz[u][2] += siz[v][2];
    }
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i), cnt[a[i]]++;
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    dfs(1, 0);
    cout << ans << endl;
    return 0;
}
```
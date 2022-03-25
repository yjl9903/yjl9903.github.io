---
mathjax: true
title: The 2018 ICPC Asia QingDao Regional Contest 总结
date: 2018-11-07 16:06:55
tags:
- Onsite
categories:
- 训练
- 现场赛
---

银牌！

|    rank    | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  |
| :--------: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|   78/374   |    8   |  .  |  Ø  |  O  |  O  |  O  |  !  |  .  |  .  |  .  |  O  |  Ø  |  Ø  |  O  |

<!--more-->

# C

Solved by wb.

# D

Solved by XLor.

暴搜。

# E

Solved by wb.

二分答案，贪心构造，注意构造时停留位置等细节。

# F

Unsolved by Archger.

# J

Solved by Archger and XLor.

忘记考虑 0，wa了一发。

# M

Solved by XLor and wb.

# 代码

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000000 + 5;

int n; char s[maxn], t[maxn];

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%d%s%s", &n, s, t);
        vector<int> v; int cnt = 0, s1 = 0, s2 = 0;
        for (int i = 0; i < n; i++){
            if (s[i] == t[i]){
                cnt++; s1++;
            }
            else {
                if (cnt) v.push_back(cnt);
                cnt = 0; s2++;
            }
        }
        if (cnt) v.push_back(cnt);
        if (v.size() == 0) printf("%d\n", 2 * s2 - 2);
        else if (v.size() == 1){
            if (s[0] != t[0] && s[n - 1] != t[n - 1]) puts("6");
            else if (s[0] != t[0] || s[n - 1] != t[n - 1]) printf("%d\n", 2 * s2 + 2 * s1 - 2);
            else printf("%lld\n", 1ll * s1 * (s1 + 1) / 2);
        }
        else if (v.size() == 2){
            if (s[0] != t[0] && s[n - 1] != t[n - 1]) puts("0");
            else if (s[0] != t[0] || s[n - 1] != t[n - 1]) puts("6");
            else printf("%d\n", 2 * s1 + 2 * s2 - 2);
        }
        else if (v.size() == 3){
            if (s[0] == t[0] && s[n - 1] == t[n - 1]) puts("6");
            else puts("0");
        }
        else puts("0");
    }
    return 0;
}
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 200000 + 5;

int n, m, len, tot;
char s[maxn], a[maxn], b[maxn];

int dfs2(int i, int j){
    if (i == n && j == len) return 1;
    else if (i == n) return 0;
    else if (j == len) return 0;
    for (int k = 0; k <= 9; k++){
        a[i] = k + '0';
        int tj = j, flag = 1;
        for (int l = 0; l < m; l++){
            int x = k * (b[l] - '0');
            if (x < 10){
                if (s[tj] != x + '0') {
                    flag = 0; break;
                }
                tj++;
            }
            else {
                if (s[tj] - '0' != x / 10 || s[tj + 1] - '0' != x % 10){
                    flag = 0; break;
                }
                tj += 2;
            }
        }
        if (flag && dfs2(i + 1, tj)) return 1;
    }
    return 0;
}
int dfs(int i, int j){
    if (i == m) return j;
    if (j == len) return 0;
    int x = s[j] - '0';
    if (x % tot == 0){
        b[i] = '0' + (x / tot);
        int r = dfs(i + 1, j + 1);
        if (r) return r;
    }
    if (x && j + 1 < len){
        int y = s[j + 1] - '0';
        x = x * 10 + y;
        if (x % tot == 0 && x / tot <= 9){
            b[i] = '0' + (x / tot);
            int r = dfs(i + 1, j + 2);
            if (r) return r;
        }
    }
    return 0;
}

int main(){
    int T; scanf("%d", &T);
    while (T--){
        ms(a, 0); ms(b, 0);
        scanf("%d%d%s", &n, &m, s); len = strlen(s);
        if (1ll * n * m > len){
            puts("Impossible"); continue;
        }
        int flag = 0;
        for (int i = 1; i <= 9; i++){
            a[0] = i + '0'; tot = i;
            int r = dfs(0, 0);
            if (r){
                // cout << b << endl;
                if (dfs2(1, r)) {
                    flag = 1; break;
                }
            }
        }
        if (flag) printf("%s %s\n", a, b);
        else puts("Impossible");
    }
    return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

int n, a[maxn]; ll m, b[maxn];

inline ll ceil(ll x, ll y){ return x % y == 0 ? x / y : x / y + 1; }
bool check(ll x){
    for (int i = 1; i <= n; i++) b[i] = ceil(x, a[i]);
    ll ans = 1; b[1]--;
    for (int i = 1; i < n; i++){
        ans += 2 * b[i] + 1;
        if (i == n - 1){
            if (b[i + 1] <= b[i]) ans--;
            else ans += 2 * (b[i + 1] - b[i] - 1);
        }
        if (ans > m) return 0;
        b[i + 1] = max(0ll, b[i + 1] - b[i] - 1);
    }
    return ans <= m;
}

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%d%lld", &n, &m);
        for (int i = 1; i <= n; i++) scanf("%d", a + i);
        ll l = 0, r = 1e13, ans = 0;
        while (l <= r){
            ll m = (l + r) / 2;
            if (check(m)) ans = m, l = m + 1;
            else r = m - 1;
        }
        printf("%lld\n", ans);
    }
    return 0;
}
```

## J

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

int n, m, a[maxn];

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%d%d", &n, &m); int cnt = 0; vector<int> v;
        for (int i = 0; i < n; i++){
            scanf("%d", a + i);
            if (!a[i]) cnt++;
            else v.push_back(a[i]);
        }
        if (n == m) puts("Richman");
        else if (cnt > m) puts("Impossible");
        else {
            m -= cnt;
            ll sum = 0, mn = v[m]; 
            for (int i = 0; i < m; i++) sum += v[i];
            for (int i = m; i < v.size(); i++) mn = min(mn, 1ll * v[i]);
            printf("%lld\n", sum + max(mn - 1, 0ll));
        }
    }
    return 0;
}
```

## M

```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 100000 + 5;

int x, k, f[20] = {1, 0, 0, 0, 1, 0, 1, 0, 2, 1};

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%d%d", &x, &k);
        int ans = x, i;
        for (i = 0; i < k; i++){
            int t = ans, sum = 0;
            if (t == 0) sum = 1;
            while (t){
                sum += f[t % 10]; t /= 10;
            }
            ans = sum;
            if (sum == 0) break;
        }
        if (i == k) printf("%d\n", ans);
        else printf("%d\n", (k - i + 1) % 2);
    }
    return 0;
}
```
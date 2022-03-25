---
mathjax: true
title: Educational Round 19 题解
tags:
  - Codeforces
  - Solution
  - Sqrt
  - Tree
categories:
  - Codeforces
  - Educational Round
date: 2018-09-29 12:23:05
---

# A k-Factorization

对 $n$ 质因数分解。

# B Odd sum

将所有正偶数加在一起。

排序后从后往前加奇数，判断和是否为奇数，更新最大值。

# C Minimal string

预处理 s 串的每个后缀最小值和后缀最小值出现的位置。

遍历 s 串，找到后缀最小值，如果当前中间栈顶小于等于后缀最小值，则压入答案中。

之后将当前位置到后缀最小值全部压入中间栈内。

时间复杂度：$O(n)$。

# D Broken BST

对于一棵排序二叉树，每一个节点都有一条从根到当前节点的路径，这条路径决定了一个节点的最大值和最小值，如果这个节点在这个范围内，那么代表他可以通过这颗排序二叉树找到。

dfs遍历这棵树，维护路径上的最大值和最小值即可，用 set 记录可以到达的节点。

时间复杂度：$O(n+n\log(n))$。

# E Array Queries

首先注意到一种时间复杂度 $O(n^2)$ 暴力的方法。

第二，注意一种 dp 的方法，时空复杂度均为 $O(n^2)$ 。

但是发现如果 $k > \sqrt{n}$ ，那么第一种暴力的方法只需要 $O(\sqrt{n})$ 的时间。

而对于 $k \le \sqrt{n}$ ，使用第二种 dp 的方法，时空复杂度将为 $O(n\sqrt{n})$。

总体时间复杂度为 $O(n\sqrt{n} + q\sqrt{n})$ 。

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
const int maxn = 1000 + 5;

int n, k;
vector<int> v;

int main(){
    cin >> n >> k; int m = n;
    for (int i = 2, flag = 1; i <= n && flag; i++){
        if (n % i == 0){
            while (n % i == 0){
                v.push_back(i);
                n /= i;
            }
            if (v.size() > k) break;
        }
    }
    if (v.size() < k) {
        puts("-1");
    }
    else {
        int tot = 1;
        for (int i = 0; i < k - 1; i++){
            printf("%d ", v[i]); tot *= v[i];
        }
        printf("%d", m / tot);
    }
    return 0;
}
```

## B

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;
const ll inf = 1ll << 60;

int n, a[maxn];

int main(){
    scanf("%d", &n); for (int i = 0; i < n; i++) scanf("%d", a + i);
    sort(a, a + n); 
    ll sum1 = 0, sum2 = 0, maxs = -inf;
    for (int i = n - 1; i >= 0; i--){
        if (a[i] > 0 && a[i] % 2 == 0){ 
            sum2 += 1ll * a[i];
        }
        else if (abs(a[i]) % 2 == 1){
            sum1 += 1ll * a[i];
            if (abs(sum1) % 2ll == 1ll && sum1 >= maxs) maxs = sum1;
        }
    }
    printf("%I64d", maxs + sum2);
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <string>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

char s[maxn], m[maxn];
int n, p[maxn];

int main(){
    scanf("%s", s); n = strlen(s);
    string tmp, ans;
    m[n - 1] = s[n - 1]; p[n - 1] = n - 1;
    for (int i = n - 2; i >= 0; i--){
        if (s[i] <= m[i + 1]){
            m[i] = s[i]; p[i] = i;
        }
        else {
            m[i] = m[i + 1]; p[i] = p[i + 1];
        }
    }
    for (int i = 0; i < n;){
        char ch = m[i]; int pos = p[i];
        while (tmp.size() && ch >= tmp.back()){
            ans.push_back(tmp.back()); tmp.pop_back();
        }
        for (; i <= pos; i++) tmp.push_back(s[i]);
        ans.push_back(tmp.back()); tmp.pop_back();
    }
    while (!tmp.empty()){
        char ch = tmp.back(); 
        tmp.pop_back(); ans.push_back(ch);
    }
    cout << ans;
    return 0;
}
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;
const int inf = 1 << 30;

int n, root, val[maxn], ch[maxn][2], in[maxn], ans = 0;
set<int> st;

void dfs(int u, int mi, int mx){
    if (mi < val[u] && val[u] < mx) st.insert(val[u]);
    if (ch[u][0] != -1) dfs(ch[u][0], mi, min(mx, val[u]));
    if (ch[u][1] != -1) dfs(ch[u][1], max(mi, val[u]), mx);
}

int main(){
    scanf("%d", &n);
    for (int i = 1; i <= n; i++){
        scanf("%d%d%d", &val[i], &ch[i][0], &ch[i][1]);
        if (ch[i][0] != -1) in[ch[i][0]]++;
        if (ch[i][1] != -1) in[ch[i][1]]++;
    }
    for (int i = 1; i <= n; i++) if (!in[i]){
        root = i; break;
    }
    dfs(root, -1, inf);
    for (int i = 1; i <= n; i++) if (!st.count(val[i])) ans++;
    printf("%d", ans);
    return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;
const int maxm = 400;

int n, q, a[maxn];
int dp[maxn][maxm];

int main(){
    scanf("%d", &n); for (int i = 1; i <= n; i++) scanf("%d", a + i);
    int m = sqrt(1.0 * n);
    for (int j = 1; j <= m; j++){
        for (int i = n; i > 0; i--){
            if (i + a[i] + j > n) dp[i][j] = 1;
            else dp[i][j] = dp[i + a[i] + j][j] + 1;
        }
    }
    scanf("%d", &q);
    while (q--){
        int p, k; scanf("%d%d", &p, &k);
        if (k <= m){
            printf("%d\n", dp[p][k]);
        }
        else {
            int ans = 0;
            while (p <= n){
                p += a[p] + k;
                ans++;
            }
            printf("%d\n", ans);
        }
    }
    return 0;
}
```


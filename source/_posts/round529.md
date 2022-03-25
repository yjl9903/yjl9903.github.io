---
mathjax: true
title: Codeforces Round 529 题解
tags:
  - Codeforces
  - Solution
  - Graph
categories:
  - Codeforces
date: 2018-12-30 02:10:18
---

First AK Div3...

# A Repeating Cipher

模拟。

# B Array Stabilization

序列删掉一个数后最小极差。

显然要么删除最后一个，要么删除第一个。

# C Powers Of Two

构造，将 $n$ 划分成恰好 $k$ 个 $2$ 的幂次数之和。

我的构造方法：将 $n$ 二进制分解，从最高位往最低位拆，看能不能拆成 $k$ 个。

# D Circular Dance

$n$ 个人围成一个环，每个人知道自己前面的两个人是谁，要求恢复出原来的环，保证有解。

实际上是已知了图上的 $n$ 条边，建图 dfs，把环跑出来，特判一下绕行的正反方向。

# E Almost Regular Bracket Sequence

给定一个括号序列，数一下有多少个位置翻转后可以使括号序列平衡。

一个括号序列平衡的条件是，对于其每个前缀，左括号数量都大于等于右括号数量，并且最终括号总数相同。

那么可以将左括号设置 $+1$，右括号设置为 $-1$，跑一下序列的前后缀和，然后判断这个位置和其前后缀和加起来是否能够等于 $0$ 即可。

但是这里实际上不能保证满足第一个条件，可以再维护一下前后缀到多少满足这个条件，如果当前位置的子串包含这些非法的前后缀，则跳过。

注意一个细节：左括号变成右括号时，前缀和不能为 $0$。（因为要匹配新加入的括号，不过我的写法好像不需要特判？）

# F Make It Connected

给定 $n$ 个点的点权，两个点连边的代价是两个点的点权之和，给定 $m$ 条边，这条边可以选择使用点权和或者这条边权，求最小生成树。

不考虑 $m$ 条边，实际上只需要从点权最小的点连向每一个点即可。

考虑 $m$ 条边，和点权最小的点连出的边一起加到最小堆内，跑一遍 Kruskal。

<!--more-->

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

int n; char s[maxn];

int main(){
    cin >> n >> s + 1;
    for (int i = 1, j = 1; i <= n; i += j, j++) {
        putchar(s[i]);
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
const int maxn = 200000 + 5;

int n, a[maxn];

int main(){
    scanf("%d", &n); for (int i = 0; i < n; i++) scanf("%d", a + i);
    sort(a, a + n);
    int d1 = a[n - 2] - a[0], d2 = a[n - 1] - a[1];
    cout << min(d1, d2);
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

int n, k, cnt[40];

void out() {
    puts("YES");
    for (int i = 0, j = 1; i < 31; i++, j += j) {
        while (cnt[i]--) printf("%d ", j);
    }
}

int main(){
    cin >> n >> k;
    if (n < k) return puts("NO"), 0;
    if (n == 1) return puts("YES\n1"), 0;
    int sum = 0, last = 0;
    for (int i = 0, j = 1; i < 31; i++, j += j) {
        if (j & n) cnt[i]++, sum++, last = i;
    }
    if (k < sum) return puts("NO"), 0;
    for (int i = last; i > 0; i--) {
        if (sum == k) return out(), 0;
        if (sum + cnt[i] >= k) {
            cnt[i] -= k - sum;
            cnt[i - 1] += 2 * (k - sum);
            out(); return 0;
        }
        sum += cnt[i];
        cnt[i - 1] += 2 * cnt[i];
        cnt[i] = 0;
    }
    puts("NO");
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
const int maxn = 200000 + 5;

int n, a[maxn][2], vis[maxn];
int pos[maxn][2];
vector<int> edge[maxn];
vector<int> ans;

void dfs(int u, int f) {
    for (int& v: edge[u]) {
        if (v == f || vis[v]) continue;
        ans.push_back(v); vis[v] = 1;
        dfs(v, u);
    }
}

int main(){
    scanf("%d", &n);
    for (int i = 1, x, y; i <= n; i++) {
        scanf("%d%d", &x, &y);
        pos[i][0] = x; pos[i][1] = y;
        edge[x].push_back(y);
        edge[y].push_back(x);
    }
    dfs(1, 0);
    if (ans[1] != pos[ans[0]][0] && ans[1] != pos[ans[0]][1]) reverse(ans.begin(), ans.end());
    // reverse(ans.begin(), ans.end());
    for (int& x: ans) printf("%d ", x);
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
const int maxn = 1000000 + 5;

int n, pre[maxn], isp[maxn], suf[maxn], iss[maxn]; char s[maxn];

int main(){
    scanf("%d%s", &n, s + 1);
    isp[0] = iss[n + 1] = 1;
    for (int i = 1; i <= n; i++) {
        pre[i] = pre[i - 1];
        if (s[i] == '(') pre[i]++;
        else pre[i]--;
        if (pre[i] >= 0 && isp[i - 1]) isp[i] = 1;
    }
    for (int i = n; i >= 1; i--) {
        suf[i] = suf[i + 1];
        if (s[i] == '(') suf[i]++;
        else suf[i]--;
        if (suf[i] <= 0 && iss[i + 1]) iss[i] = 1;
    }
    int ans = 0;
    for (int i = 1; i <= n; i++) {
        if (!isp[i - 1] || !iss[i + 1]) continue;
        if (s[i] == '(') {
            if (/* pre[i - 1] > 0 &&  */pre[i - 1] + suf[i + 1] - 1 == 0) ans++;
        } else {
            if (pre[i - 1] + suf[i + 1] + 1 == 0) ans++;
        }
    }
    cout << ans;
    return 0;
}
```

## F

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <map>
#include <queue>
#include <vector>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int maxn = 200000 + 5;

struct edge {
    int u, v; ll d;
    bool operator<(const edge& b) const {
        return d > b.d;
    }
};

int n, m, pre[maxn];
ll a[maxn];

int find(int x) { return x == pre[x] ? x : pre[x] = find(pre[x]); }

int main(){
    scanf("%d%d", &n, &m); for (int i = 1; i <= n; i++) pre[i] = i;
    for (int i = 1; i <= n; i++) scanf("%I64d", a + i);
    priority_queue<edge> q;
    while (m--) {
        int x, y; ll d; scanf("%d%d%I64d", &x, &y, &d);
        q.push({x, y, d});
    }
    ll mn = 1e18; int id = 0;
    for (int i = 1; i <= n; i++) if (a[i] < mn) mn = a[i], id = i;
    for (int i = 1; i <= n; i++) if (i != id) q.push({i, id, a[i] + a[id]});
    ll ans = 0; int cnt = 0;
    while (!q.empty() && cnt < n - 1) {
        edge t = q.top(); q.pop();
        if (find(t.u) == find(t.v)) continue;
        ans += t.d;
        pre[find(t.u)] = find(t.v);
        cnt++;
    }
    cout << ans;
    return 0;
}
```
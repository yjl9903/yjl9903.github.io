---
mathjax: true
title: Codeforces Round 316 题解
tags:
  - Codeforces
  - Solution
  - Tree
categories:
  - Codeforces
date: 2018-11-09 00:45:20
---

# A Elections

仔细读题。

# B Simple Game

给定一个区间 $[1,n]$，两个人分别选定一个数 $a,m$，在范围内随机生成一个数 $x$，距离这个数近的胜利，距离相等对方胜利。

已知区间长度和对方的选择，问自己选择什么获胜概率最大。

计算一下可以得到：$2(a-m)x>(a-m)(a+m)$，对 $a,m$ 大小关系讨论后，发现 $a$ 只会取 $m+1$ 和 $m-1$，分类算一下即可。

注意特判 $n=1$。

# C Replacement

给一个字符串，每次将最前面的 ".." 合并为 "."，询问将某个位置永久修改成另外一个字符时的操作次数。

分类之后进行修改，讨论一下可以 $O(1)$ 更新答案。

# D Tree Requests

树上启发式合并。

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
const int maxn = 100 + 5;

int n, m, a[maxn][maxn];
int v[maxn], c[maxn];

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= m; i++) for (int j = 1; j <= n; j++) scanf("%d", &a[i][j]);
    for (int i = 1; i <= m; i++){
        int mx = -1;
        for (int j = 1; j <= n; j++){
            if (a[i][j] > mx) v[i] = j, mx = a[i][j];
        }
        c[v[i]]++;
    }
    int mx = -1, ans = 0;
    for (int i = 1; i <= n; i++) if (c[i] > mx) ans = i, mx = c[i];
    cout << ans;
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
const int maxn = 1000 + 5;

int n, m;

int main(){
    cin >> n >> m;
    if (n == 1) return puts("1"), 0;
    if (n - m > m - 1) printf("%d\n", m + 1);
    else printf("%d\n", m - 1);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 300000 + 5;

int n, m; char s[maxn];

int main(){
    scanf("%d%d%s", &n, &m, s + 1);
    int l = 1, r = 0, ans = 0;
    for (int i = 1; i <= n + 1; i++){
        if (s[i] == '.'){
            r++;
        } 
        else {
            if (r > l) ans += r - l;
            l = i + 1; r = i;
        }
    }
    // cout << ans << endl;
    while (m--){
        int p; char ch[2]; scanf("%d%s", &p, ch);
        if (s[p] == '.' && ch[0] == '.');
        else if (s[p] != '.' && ch[0] != '.');
        else if (s[p] == '.'){
            s[p] = ch[0];
            if (s[p - 1] != '.' && s[p + 1] != '.');
            else if (s[p - 1] == '.' && s[p + 1] == '.') ans -= 2;
            else ans--;
        }
        else {
            s[p] = ch[0];
            if (s[p - 1] == '.' && s[p + 1] == '.') ans += 2;
            else if (s[p - 1] != '.' && s[p + 1] != '.');
            else ans++;
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
#include <algorithm>
#include <vector>
#include <set>
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 500000 + 5;

vector<int> edge[maxn];
set<int> que[maxn];
map<int,int> ans[maxn];
char s[maxn];
int n, q, v[maxn], h[maxn];

int sz[maxn], son[maxn], dep[maxn];
void getsz(int u, int f){
    sz[u] = 1; dep[u] = dep[f] + 1;
    for (auto& v : edge[u]){
        if (v == f) continue;
        getsz(v, u); sz[u] += sz[v];
        if (sz[v] > sz[son[u]]) son[u] = v;
    }
}

int sk[maxn], cnt[maxn][30];
bool check(int d){
    int flag = 0;
    for (int i = 0; i < 26; i++){
        if (cnt[d][i] & 1) flag++;
    }
    return flag <= 1;
}
void add(int u, int f, int x){
    cnt[dep[u]][s[u] - 'a'] += x;
    for (auto& v : edge[u]){
        if (v == f || sk[v]) continue;
        add(v, u, x);
    }
}
void dfs(int u, int f, int kp){
    for (auto& v : edge[u]){
        if (v == f || v == son[u]) continue;
        dfs(v, u, 0);
    }
    if (son[u]) dfs(son[u], u, 1), sk[son[u]] = 1;
    add(u, f, 1); sk[son[u]] = 0;

    for (auto& x : que[u]){
        if (check(x)) ans[u][x] = 1;
    }

    if (!kp) add(u, f, -1);
}

int main(){
    scanf("%d%d", &n, &q);
    for (int i = 2, x; i <= n; i++) scanf("%d", &x), edge[x].push_back(i);
    scanf("%s", s + 1);
    for (int i = 1; i <= q; i++){
        scanf("%d%d", v + i, h + i);
        que[v[i]].insert(h[i]);
    }
    getsz(1, 0); dfs(1, 0, 1);
    for (int i = 1; i <= q; i++){
        // printf("%d\n", ans[v[i]][h[i]]);
        if (ans[v[i]][h[i]]) puts("Yes");
        else puts("No");
    }
    return 0;
}
```

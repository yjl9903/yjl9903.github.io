---
mathjax: true
title: Educational Round 3 题解
tags:
  - Codeforces
  - Solution
  - Geometry
  - Tree
categories:
  - Codeforces
  - Educational Round
date: 2018-11-09 00:54:09
---

# A Extract Numbers

字符串处理。

# B Queries about less or equal elements

二分。

# C Make Palindrome

特判一下奇数个数字符，对应修改。

# D Area of Two Circles' Intersection

两个圆的面积交。

卡double，毒瘤。

# E Lomsat gelral

树上启发式合并。

<!--more-->

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <string>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

char s[maxn]; int n;

bool check(string s){
    if (s.empty()) return 1;
    if (s == "0") return 0;
    if (s[0] < '1' || s[0] > '9') return 1;
    for (int i = 1; i < s.length(); i++) if (s[i] < '0' || s[i] > '9') return 1;
    return 0;
}

int main(){
    scanf("%s", s); n = strlen(s);
    vector<string> v[2];
    string tmp("");
    for (int i = 0; i < n; i++){
        if (s[i] == ',' || s[i] == ';') {
            v[check(tmp)].push_back(tmp);
            tmp = ""; continue;
        }
        tmp.push_back(s[i]);
    }
    v[check(tmp)].push_back(tmp);
    if (v[0].empty()) cout << '-' << endl;
    else {
        cout << '"';
        for (int i = 0; i < v[0].size(); i++){
            if (i) cout << ',';
            cout << v[0][i];
        }
        cout << '"' << endl;
    }
    if (v[1].empty()) cout << '-' << endl;
    else {
        cout << '"';
        for (int i = 0; i < v[1].size(); i++){
            if (i) cout << ',';
            cout << v[1][i];
        }
        cout << '"' << endl;
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

int n, m, a[maxn];

int main(){
    scanf("%d%d", &n, &m);
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    sort(a, a + n);
    while (m--){
        int x; scanf("%d", &x);
        int r = upper_bound(a, a + n, x) - a;
        printf("%d ", r);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 200000 + 5;

char s[maxn]; int n, cnt[30];

void print(){
    int flag = -1;
    for (int i = 0; i < 26; i++){
        if (cnt[i] & 1) flag = i;
        int x = cnt[i] / 2;
        while(x--) putchar(i + 'a');
    }
    if (flag != -1) putchar(flag + 'a');
    for (int i = 25; i >= 0; i--){
        int x = cnt[i] / 2;
        while(x--) putchar(i + 'a');
    }
}

int main(){
    scanf("%s", s); n = strlen(s);
    for (int i = 0; i < n; i++) cnt[s[i] - 'a']++;
    vector<int> v;
    for (int i = 0; i < 26; i++){
        if (cnt[i] & 1) v.push_back(i);
    }
    // if (v.size() <= 1) return print(), 0;
    for (int i = 0; i < v.size() / 2; i++){
        cnt[v[i]]++;
        cnt[v[v.size() - 1 - i]]--;
    }
    print();
    return 0;
}
```

## D

```c++
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef long double lld;
const int maxn = 1000 + 5;
const double eps = 1e-8;
const double pi = acos(-1.0);

lld x1, y1, r1, x2, y2, r2;

struct Point{
    lld x, y;
    Point(lld x = 0, lld y = 0): x(x), y(y){}
};

inline int dcmp(lld x){
    if (fabs(x) < eps) return 0;
    else return x < 0 ? -1 : 1;
}

lld dis(Point a, Point b){ a.x -= b.x; a.y -= b.y; return sqrt(a.x * a.x + a.y * a.y); }

int main(){
    cin >> x1 >> y1 >> r1 >> x2 >> y2 >> r2;
    Point a(x1, y1), b(x2, y2);
    lld d = dis(a, b), minr = min(1.0 * r1, 1.0 * r2);
    if (dcmp(d - 1.0 * r1 - 1.0 * r2) >= 0) return printf("%.10lf", 0.0), 0; 
    if (dcmp(d - fabs(1.0 * r1 - 1.0 * r2)) <= 0) return printf("%.10lf", double(pi * minr * minr)), 0;

    lld a1 = 2*acos((1.0*r1*r1+d*d-1.0*r2*r2)/(2.0*r1*d));
    lld a2 = 2*acos((1.0*r2*r2+d*d-1.0*r1*r1)/(2.0*r2*d));
    lld ans = 1.0*r1*r1*a1/2.0 + 1.0*r2*r2*a2/2.0 - 1.0*r1*r1*sin(a1)/2.0 - 1.0*r2*r2*sin(a2)/2.0;
    printf("%.10lf", (double)ans);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

vector<int> edge[maxn];
int n, c[maxn];
ll ans[maxn], cnt[maxn];

int sz[maxn], son[maxn];
void getsz(int u, int f){
    sz[u] = 1; 
    for (auto& v : edge[u]){
        if (v == f) continue;
        getsz(v, u); sz[u] += sz[v];
        if (sz[v] > sz[son[u]]){
            son[u] = v;
        }
    }
}
ll res = 0; int mx = 0; bool sk[maxn];
void add(int u, int f, int x){
    cnt[c[u]] += x;
    if (x > 0 && cnt[c[u]] >= mx){
        if (cnt[c[u]] > mx) res = 0, mx = cnt[c[u]];
        res += c[u];
    }
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
    add(u, f, 1);
    ans[u] = res;
    if (son[u]) sk[son[u]] = 0;
    if (!kp) add(u, f, -1), res = mx = 0;
}

int main(){
    scanf("%d", &n); for (int i = 1; i <= n; i++) scanf("%d", c + i);
    for (int i = 1; i < n; i++){
        int x, y; scanf("%d%d", &x, &y);
        edge[x].push_back(y); edge[y].push_back(x);
    }
    getsz(1, 0); dfs(1, 0, 0);
    for (int i = 1; i <= n; i++) printf("%I64d ", ans[i]);
    return 0;
}
```
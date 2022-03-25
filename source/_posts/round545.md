---
mathjax: true
title: Codeforces Round 545 题解
tags:
  - Codeforces
  - Solution
  - String
  - KMP
  - Interaction
categories:
  - Codeforces
date: 2019-03-09 22:34:54
---

# A Sushi for Two

给定一个序列，求最长的一个子串，满足 $00 \dots 011 \dots 1$。

预处理每个位置后的最长连续 $1$ 或 $2$，扫一遍即可。

# B Circus

有 $n$ 个人，每个人可以表演 a 或 c 两种节目，将 $n$ 个人分成大小相同的两块，满足第一块能够表演 a 的人和第二块表演能够 c 的人数相同。

一共有四类，两种都无法表演，表演一种和两种都不能表演。

枚举第一块表演 a 的个数和第二块表演 c 的个数，解一个关于两种都行的方程即可。

注意各种边界条件。

# C Skyscrapers

给定一个 $n \times m$ 的矩阵，独立询问每个位置所在行和列，用数字 $1$ 到 $x$ 去替换这一行一列的数字，使得原来的行内数字大小关系不变，列内数字大小关系不变。

对每一行和每一列分别离散化，询问就是行列比当前位置的数小的个数的最大值，以及对应大的最大值，最后加 $1$。

# D Camp Schedule

给定 $01$ 串 $s$ 和 $t$，要求任意调换 $s$ 串内的顺序，使得新串包含最多的子串 $t$。

对 $t$ 串建 $KMP$ 的 $fail$ 指针，扫一遍贪心构造即可。 

# F Cooperative Game

有一个长度为 $t+c$ 的有向链，其中 $t+c$ 连向 $t+1$。

起点为 $1$，终点为 $t+1$，一开始有 $10$ 个人在起点。

每次可以移动一些点到其对应的下一个位置，返回哪些点在相同位置。

要求在 $3 \times (t+c)$ 次操作内将这些人移动到起点。

龟兔赛跑判环。

取两个点，每次一个点走 $1$ 步，一个点走 $2$ 步，设最后相遇在第 $x$ 步

$$
x-t \equiv 2x-t (mod \text{ } c)
$$

因此，$c|x$，然后起点和相遇点一同往后移动，显然第一次相遇在 $t$ 处。

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
const int maxn = 100000 + 5;

int n, a[maxn], suf[maxn];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    for (int i = n; i >= 1; i--) {
        if (a[i] == 2) suf[i] = suf[i + 1] + 1;
        else suf[i] = 0;
    }
    int ans = 0, tot = 0;
    for (int i = 1; i <= n; i++) {
        if (a[i] == 1) {
            tot++;
            ans = max(ans, min(tot, suf[i + 1]));
        } else tot = 0;
    }
    reverse(a + 1, a + 1 + n);
    for (int i = n; i >= 1; i--) {
        if (a[i] == 2) suf[i] = suf[i + 1] + 1;
        else suf[i] = 0;
    }
    tot = 0;
    for (int i = 1; i <= n; i++) {
        if (a[i] == 1) {
            tot++;
            ans = max(ans, min(tot, suf[i + 1]));
        } else tot = 0;
    }
    cout << ans * 2 << endl;
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
const int maxn = 100000 + 5;

int n; char c[maxn], a[maxn];

int c1, c2, c3, c4;
int check(int x, int y) {
    if (y - x + c4 < 0) return 0;
    if ((y - x + c4) % 2) return 0;
    
    int k = (y - x + c4) / 2;
    int a2 = x, a3 = c3 - y, a4 = k, a1 = n / 2 - a2 - a3 - a4;

    if (k > c4) return 0;
    if (a1 < 0 || a1 > c1) return 0;

    for (int i = 1; i <= n; i++) {
        if (c[i] == '0' && a[i] == '0') {
            if (a1) printf("%d ", i), a1--;
        }
        if (c[i] == '1' && a[i] == '0') {
            if (a2) printf("%d ", i), a2--;
        }
        if (c[i] == '0' && a[i] == '1') {
            if (a3) printf("%d ", i), a3--;
        }
        if (c[i] == '1' && a[i] == '1') {
            if (a4) printf("%d ", i), a4--;
        }
    }
    return 1;
}

int main() {
    scanf("%d%s%s", &n, c + 1, a + 1);
    for (int i = 1; i <= n; i++) {
        if (c[i] == '0' && a[i] == '0') c1++;
        if (c[i] == '1' && a[i] == '0') c2++;
        if (c[i] == '0' && a[i] == '1') c3++;
        if (c[i] == '1' && a[i] == '1') c4++;
    }
    for (int i = 0; i <= c2; i++) for (int j = 0; j <= c3; j++) if (check(i, j)) return 0;
    puts("-1");
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 1000 + 5;

int n, m, a[maxn][maxn], ans[maxn][maxn];
vector<int> row[maxn], col[maxn];

void init(vector<int>& v) {
    sort(v.begin(), v.end());
    v.resize(unique(v.begin(), v.end()) - v.begin());
}
int gid(int x, vector<int>& v) {
    return lower_bound(v.begin(), v.end(), x) - v.begin() + 1;
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++) {
        scanf("%d", &a[i][j]);
        row[i].push_back(a[i][j]);
        col[j].push_back(a[i][j]);
    }
    for (int i = 1; i <= n; i++) init(row[i]);
    for (int i = 1; i <= m; i++) init(col[i]);
    for (int i = 1; i <= n; i++, puts("")) for (int j = 1; j <= m; j++) {
        int x = gid(a[i][j], row[i]);
        int y = gid(a[i][j], col[j]);
        printf("%d ", 1 + max(x - 1, y - 1) + max((int)row[i].size() - x, (int)col[j].size() - y));
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 500000 + 5;

char s[maxn], t[maxn], ans[maxn];
int nxt[maxn];

void getnxt() {
    int len = strlen(t), i = 0, j = -1; nxt[0] = -1;
    while (i < len) {
        if (j == -1 || t[i] == t[j]) i++, j++, nxt[i] = j;
        else j = nxt[j];
    }
}

int main() {
    scanf("%s%s", s, t);
    getnxt();
    int c[2] = {0, 0};
    for (int i = 0; s[i]; i++) c[s[i] - '0']++;
    int slen = strlen(s), tlen = strlen(t), i = 0, j = 0;
    while (i < slen && j < tlen) {
        if (j == -1) {
            if (c[t[0] - '0']) {
                ans[i] = t[0]; c[t[0] - '0']--;
            } else {
                ans[i] = (t[0] - '0' + 1) % 2 + '0';
                c[ans[i] - '0']--;
            }
            i++; j++;
        } else if (c[t[j] - '0']) {
            ans[i] = t[j]; c[t[j] - '0']--;
            i++; j++;
        } else j = nxt[j];
        if (j == tlen) j = nxt[j];
    }
    cout << ans << endl;
    return 0;
}
```

## F

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
const int maxn = 100000 + 5;

string query(vector<int> v) {
    cout << "next";
    for (int& x: v) cout << ' ' << x;
    cout << endl;
    string s; getline(cin, s);
    return s;
}

int main() {
    while (true) {
        query({0, 1});
        string s = query({1});
        if (s.find("01") != string::npos) break;
    }
    vector<int> v = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
    while (true) {
        string s = query(v);
        if (s.find("0123456789") != string::npos) break;
    }
    cout << "done" << endl;
    return 0;
}
```
---
mathjax: true
title: Codeforces Round 534 题解
tags:
  - Codeforces
  - Solution
  - Math
categories:
  - Codeforces
date: 2019-01-23 22:18:43
---

下分大失败？

# A Splitting into digits

输出 $n$ 个 $1$。
 
# B Game with string

栈模拟。

第一次插人失败很难受啊，还好是小号啊？

# C Grid game

一个 $4 \times 4$ 的方格图，往里面填 $1 \times 2$ 和 $2 \times 1$ 两种方块，放完后每一行，每一列填满的话就删除，要求填的时候不能无处可填，求一个方案。

考虑这样一种填法，竖着的都放在最左边，横着的放在右边，这样每进来 $2$ 个竖的都会被消掉，进来的是横的每 $4$ 个会消掉，不会产生冲突。

# D Game with modulo

猜模数 $a$，每次询问两个数 $x$ 和 $y$，得到 $x \text{ mod } a$ 和 $y \text{ mod } a$ 的大小关系。

如果 $x > a$，那么 $ x \text{ mod } a < {1 \over 2}x$，因此可以先倍增求出 $a$ 的范围，在这个范围内二分即可。

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
    scanf("%d", &n);
    printf("%d\n", n);
    for (int i = 1; i <= n; i++) putchar('1'), putchar(' ');
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

int n; char s[maxn];

int main(){
    scanf("%s", s);
    n = strlen(s);
    int cnt = 0;
    vector<char> st;
    for (int i = 0; i < n; i++) {
        if (!st.empty() && st.back() == s[i]) {
            st.pop_back(); cnt++;
        } else {
            st.push_back(s[i]);
        }
    }
    if (cnt % 2) puts("Yes");
    else puts("No");
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
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

char s[maxn]; int n;

int map[5][5], delx[5], dely[5];
void clear() {
    ms(delx, 0); ms(dely, 0);
    for (int i = 1; i <= 4; i++) {
        int flag = 1;
        for (int j = 1; j <= 4; j++) if (map[i][j] == 0) flag = 0;
        if (flag) delx[i] = 1;
    }
    for (int i = 1; i <= 4; i++) {
        int flag = 1;
        for (int j = 1; j <= 4; j++) if (map[j][i] == 0) flag = 0;
        if (flag) dely[i] = 1;
    }
    for (int i = 1; i <= 4; i++) {
        if (delx[i]) for (int j = 1; j <= 4; j++) map[i][j] = 0;
        if (dely[i]) for (int j = 1; j <= 4; j++) map[j][i] = 0;
    }
}

int main(){
    scanf("%s", s); n = strlen(s);
    for (int i = 0; i < n; i++) {
        if (s[i] == '0') {
            for (int i = 1; i <= 4; i++) {
                if (map[1][i] == 0 && map[2][i] == 0) {
                    map[1][i] = map[2][i] = 1;
                    printf("1 %d\n", i); break;
                } else if (map[3][i] == 0 && map[4][i] == 0) {
                    map[3][i] = map[4][i] = 1;
                    printf("3 %d\n", i); break;
                }
            }
        } else if (s[i] == '1') {
            for (int i = 1; i <= 4; i++) {
                if (map[i][3] == 0 && map[i][4] == 0) {
                    map[i][3] = map[i][4] = 1;
                    printf("%d 3\n", i); break;
                }
            }
        }
        clear();
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
#include <string>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
const int maxn = 100000 + 5;

string s;
int query(int a, int b) {
    cout << "? " << a << " " << b << endl;
    char s[5]; cin >> s;
    return s[0] - 'x';
}

// 1 % a, 2 % a, 4 % a, ... , 2^n % a

int main(){
    ios::sync_with_stdio(false); cin.tie(0);
    while (cin >> s) {
        if (s == "end" || s == "mistake") break;
        int mx = 30;
        for (int i = 1; i <= 29; i++) {
            int x = query(1 << (i - 1), 1 << i);
            if (x == 0) {
                mx = i; break;
            }
        }
        // cout << "ha " << mx << endl;
        int l = 1 << (mx - 1), r = (1 << mx) + 1;
        while (l < r) {
            int m = (l + r) >> 1;
            // cout << m << ' ' << l << ' ' << r << endl;
            if (query(r, m) == 1) l = m + 1;
            else r = m;
        }
        cout << "! " << r << endl;
    }
    return 0;
}
```
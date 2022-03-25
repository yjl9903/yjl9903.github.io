---
mathjax: true
title: Codeforces Round 523 题解
tags:
- Codeforces
- Solution
- Probability
- Tree
- Interaction
categories:
- Codeforces
date: 2018-11-23 03:42:57
---

# A Coins

# B Views Matter

最开始想肯定是维护一个递增的序列，后面的只保留一个即可，但是测一测并不对，然后发现前面维护递增的高度的同时可以在维护每列多用了了多少块，这些多用的块可以放在后面，让一列实际上可以全部清空。

# C Multiplicity

拆出每个数的因子然后dp。

时间复杂度： $O(n\sqrt{n})$

# F Lost Root

交互题，有一棵 $n$ 个节点的满 $k$ 叉树，标号 $1-n$，现在你需要找到树的根。

每次你可以询问两个点 $a,c$ 之间的路径上是否含有 $b$。

首先想到必须要任取两个点，然后枚举其他点看是否在这条路径上，然后我们注意到任取两个点之后，我们很能够不断调整将这两个点移动到叶子上去。

因此，我们可以在 $O(n)$ 次询问内得到一条叶子结点之间的路径，然后我们又可以知道如果这条路径的长度为 $2*dep-1$，那么他必定经过根节点，然后对于根节点，恰好有 $dep-2$ 个节点在分别在两个叶子结点中间，因此如果我们能获得一条经过叶子结点的路径，就能很快计算出他的根节点。

这里我一开始考虑是不断调整，但是这个复杂度很玄学？

实际上，根据其他人提交的代码我们可以注意到一个事实，在树上任取两个点，这两个点 lca 不是根节点的概率实际上是 $\frac{k-1}{k}$，所以直接随机即可。

<!--more-->

交互题还是有趣23333.

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

int n, s;

int main(){
    cin >> n >> s;
    if (s <= n) return puts("1"), 0;
    cout << s / n + (s % n ? 1 : 0) << endl;
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

int n, a[maxn];

int main(){
    scanf("%d%*d", &n); for (int i = 1; i <= n; i++) scanf("%d", a + i);
    sort(a + 1, a + 1 + n);
    int mx = 0, rest = 0; ll ans = 0;
    for (int i = 1; i <= n; i++){
        if (a[i] > mx) ans += mx, rest += a[i] - mx - 1, mx = a[i];
        else if (a[i] > 1){
            if (rest) ans += a[i], rest--;
            else ans += a[i] - 1;
        }
    }
    cout << ans;
    return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <set>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 2000000 + 5;
const int mod = 1e9 + 7;

int n, a[maxn], dp[maxn] = {1};

int main(){
    scanf("%d", &n); 
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    int f = 0, ans = 0;
    for (int i = 0; i < n; i++, f ^= 1){
        set<int> s;
        for (int j = 1; j * j <= a[i]; j++){
            if (a[i] % j) continue;
            s.insert(j); s.insert(a[i] / j);
        }
        for (auto it = s.rbegin(); it != s.rend(); it++){
            dp[*it] = (dp[*it] + dp[(*it) - 1]) % mod;
        }
    }
    for (int i = 1; i <= n; i++) ans = (ans + dp[i]) % mod;
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
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

int rand(){
    static int seed = 233;
    return seed = int(seed * 482711ll % 2147483647);
}
int n, k;

bool ask(int a, int c, int b){
    cout << "? " << a << ' ' << b << ' ' << c << endl;
    char s[10]; cin >> s;
    return s[0] == 'Y';
}

vector<int> get(int x, int y){
    int l = x, r = y;
    for (int i = 1; i <= n; i++){
        if (i == x || i == y) continue;
        if (ask(l, r, i) == false){
            if (ask(i, r, l)) l = i;
            else if (ask(l, i, r)) r = i;
        }
    }
    vector<int> v; v.push_back(l); v.push_back(r);
    for (int i = 1; i <= n; i++){
        if (i == l || i == r) continue;
        if (ask(l, r, i)) v.push_back(i);
    }
    return v;
}

int main(){
    cin >> n >> k;
    int dep = 0;
    for (int i = 1, t = k; i <= n; i++, t *= k){
        if (n == (t - 1) / (k - 1)) {
            dep = i; break;
        }
    }
    vector<int> ans;
    // cout << dep << endl;
    while (1){
        int x = rand() % n + 1, y = rand() % n + 1;
        while (x == y) x = rand() % n + 1, y = rand() % n + 1;
        ans = get(x, y);
        if (ans.size() == 2 * dep - 1) break;
    }
    // cout << "ha" << endl;
    for (int i = 2; i < ans.size(); i++){
        int c = 0;
        for (int j = 2; j < ans.size(); j++){
            if (i == j) continue;
            if (ask(ans[0], ans[i], ans[j])) c++;
        }
        if (c == dep - 2) return cout << "! " << ans[i] << endl, 0;
    }
    return 0;
}
```
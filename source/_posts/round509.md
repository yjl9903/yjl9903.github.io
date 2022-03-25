---
mathjax: true
title: Codeforces Round 509 题解
date: 2018-09-17 00:25:38
tags:
- Codeforces
- Solution
- Binary Search
categories:
- Codeforces
---

# A Heist

排序得到左右边界，即为原编号的左右边界，减去序列的长度即可。

# B Buying a TV Set

求满足 $x_0 \le a, y_0 \le b$ 且 $\frac{x_0}{y_0}=\frac{x}{y}$ 的 $(x_0, y_0)$ 方案数，将 $\frac{x}{y}$ 约分， 答案为 $max(\frac{a}{x}, \frac{b}{y})$。

# C Coffee Break

自闭题T^T。

维护一个 set 存放所有咖啡时间点和在原序列中的下标，每次选 set 的第一个元素 $x$ 放在某一天的第一个，之后二分第一个大于等于 $x+d+1$ 的元素放在同一天并将他从 set 中删除，直到没有元素可以放在这一天。

# D Glider

注意到这样一个结论：如果我们从 $x+1$ 处下落，当 $x+1$ 位于上升气流外，假如 $x+1$ 处下落经过的气流和 $x$ 处经过的气流相同，那么他们滑翔的距离也相同，但是对于x轴的一个位置，$x+1$ 下落总比 $x$ 下落高一个单位，因此有可能经过 $x$ 未经过的气流，所以 $ans(x+1) \ge ans(x)$ ；同样的，如果 $x+1$ 位于上升气流内，$ans(x+1) \le ans(x)$。因此可以得出，最长距离一定会在某个上升气流的左端点出取到。

考虑枚举每个左端点的答案，如果直接计算显然这是 $O(n^2)$ 。

我们还发现，高度下降只会发生在上升气流之间，落地前必定高度下降 $h$ ，在上升气流之间水平移动 $h$ ，加上在经过的所有上升气流的长度和。因此，预处理上升气流长度的后缀和，上升气流之间距离的后缀和。

假设枚举到第 $i$ 个位置，如果当前高度大于当前间隔后缀和，那么表示可以滑翔到最后一个上升气流之后，那么答案就是 $len(i) + h$；如果当前高度小于等于当前间隔后缀和，我们需要找到从 $i$ 开始多少个连续间隔距离加起来大于等于 $h$，也就是后缀和小于等于 $h-delta(i)$，因为是后缀和所以满足单调性，二分即可。

时间复杂度 $O(n+n\log n)$。

<!--more-->

# 代码

### A Heist

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

int n, a[maxn];

int main(){
    scanf("%d", &n);
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    sort(a, a + n);
    int k = a[n - 1] - a[0];
    printf("%d", k + 1 - n);
    return 0;
}
```

### B Buying a TV Set

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

ll a, b, x, y;

ll gcd(ll a, ll b){return b ? gcd(b, a % b) : a;}

int main(){
    scanf("%I64d%I64d%I64d%I64d", &a, &b, &x, &y);
    ll q = gcd(x, y);
    x /= q; y /= q;
    ll k1 = a / x, k2 = b / y;
    printf("%I64d", min(k1, k2));
    return 0;
}
```

### C Coffee Break

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <utility>
#include <set>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int maxn = 200000 + 5;

int n, m, d, ans[maxn];
set<PII> s;

int main(){
    scanf("%d%d%d", &n, &m, &d);
    for (int i = 0, x; i < n; i++) scanf("%d", &x), s.insert(make_pair(x, i));
    int day = 0;
    while (!s.empty()){
        day++;
        auto it = s.begin();
        while (it != s.end()){
            ans[it->second] = day;
            int x = it->first;
            s.erase(it);
            it = s.lower_bound(make_pair(x + d + 1, 0));
        }
    }
    printf("%d\n", day);
    for (int i = 0; i < n; i++) printf("%d ", ans[i]);
    return 0;
}
```

### D Glider

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 200000 + 5;
const int inf = 1 << 30;

int n, h, l[maxn], r[maxn], dt[maxn], len[maxn];

int main(){
    scanf("%d%d", &n, &h);
    for (int i = 0; i < n; i++) scanf("%d%d", l + i, r + i);
    if (n == 1){
        printf("%d", r[0] - l[0] + h); return 0;
    }
    for (int i = n - 2; i >= 0; i--) dt[i] = dt[i + 1] - l[i + 1] + r[i];
    for (int i = n - 1; i >= 0; i--) len[i] = len[i + 1] + r[i] - l[i];
    int ans = -inf;
    for (int i = 0; i < n; i++){
        if (h > -dt[i]) ans = max(ans, h + len[i]);
        else {
            int k = lower_bound(dt, dt + n, dt[i] + h) - dt;
            ans = max(ans, h + len[i] - len[k]);
        }
    }
    printf("%d", ans);
    return 0;
}
```


---
mathjax: true
title: Altruistic Amphibians 题解
date: 2018-11-02 02:36:10
tags:
- DP
- Solution
categories:
- 题解
---

传送门：http://codeforces.com/gym/101933/problem/A

# 题面

井底之蛙想要跳出井底，已知井的深度 $d$。

给出 $n$ 个三元组 $(l_i,w_i,h_i)$ ，表示一只青蛙的跳跃高度，重量和高度。

每次青蛙可以一层一层叠成一个塔，那么最顶层的青蛙跳跃高度为 $ {l}_{i} + \sum h$，其中塔必须满足每层的青蛙重量必须大于上面所有青蛙的重量之和。每次叠成塔送顶层跳出去之后，可以立马重新堆一个新的塔送顶层青蛙跳出去。

问最多能送多少只青蛙出去。

# 分析

可以感觉到限制青蛙能否堆在一起跳出去的条件是重量限制。

先对三元组按照重量排序，倒着做，重量越大肯定越往后跳出去，最重的那个肯定最后一个跳出去。

然后处理倒数第二重的那个，他肯定至多倒数第二个跳出去，此时只剩最重的那个作为跳板跳出去，因此可以得出 $dp$ 状态。

令 $dp[i]$ 表示可以在放一个 $重量 < i$ 的青蛙在上面时，塔的最大高度。

转移方程：$dp[j]=max(dp[j+1],dp[j],dp[j+w_i]+h_i)$ ，其中 $1 \le j \le w_i$。

转移时更新答案，当且仅当 $dp[w_i+1] + l_i > d$ 。

边界条件：$dp[j]=h_0$，其中 $1 \le j \le w_0$。

这样时间复杂度显然是 $O(n \log n + n \max(w))$ ，好像不大可做啊？但是注意到题面 $\sum w \le 10^8$ ，那么实际上时间复杂度是 $O(n \log n + \sum w)$。

<!--more-->

# 代码

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;
const int maxd = 1e8 + 5;

struct node{
    int l, w, h;
    bool operator<(const node& b)const{
        return w > b.w;
    }
}a[maxn];

int n, d, dp[maxd];

int main(){
    scanf("%d%d", &n, &d);
    for (int i = 0; i < n; i++) scanf("%d%d%d", &a[i].l, &a[i].w, &a[i].h);
    sort(a, a + n); int ans = (a[0].l > d);
    for (int i = 1; i <= a[0].w; i++) dp[i] = a[0].h;
    for (int i = 1; i < n; i++){
        if (dp[a[i].w + 1] + a[i].l > d) ans++;
        for (int j = a[i].w; j >= 1; j--){
            dp[j] = max(dp[j], dp[j + 1]);
            dp[j] = max(dp[j], dp[j + a[i].w] + a[i].h);
            dp[j] = min(dp[j], (int)1e8);
        }
    }
    printf("%d\n", ans);
    return 0;
}
```


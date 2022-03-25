---
mathjax: true
title: Educational Round 51 题解
tags:
- Codeforces
- Solution
- DP
categories:
- Codeforces
- Educational Round
date: 2018-09-22 01:33:32
---

# A Vasya And Password

统计一下三种字符的个数。

如果每种有多于 $1$ 个字符，将其替换为没出现过的字符。

# B Relatively Prime Pairs

显然 $\gcd(i,i+1)=1$，直接输出即可。

# C Vasya and Multisets

统计只出现 $1$ 次的数字有哪些，如果有偶数个，对半分配即可。

出现两次的数字要么一人拿一个，要么一个人全拿，不影响答案。

出现次数大于 $2$ 的数字，如果已经可以分配了就全部给一个人即可；如果第一种是奇数个，那么分配一个出现次数大于 $2$ 的。

# D Bicolorings

自闭题T^T。

考虑 $dp[i][j][state]$ 表示遍历到第 $i$ 列，有 $j$ 个联通块，第 $i$ 列状态为 $state$ 的方案数。

时间复杂度 $O(nk)=O(n^2)$。

<!--more-->

# 代码

## A

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

char s[maxn];

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%s", s);
        int len = strlen(s);
        int a = 0, b = 0, c = 0;
        for (int i = 0; i < len; i++){
            if (s[i] >= 'a' && s[i] <= 'z') a++;
            if (s[i] >= 'A' && s[i] <= 'Z') b++;
            if (s[i] >= '0' && s[i] <= '9') c++;
        }
        if (a && b && c) {
            printf("%s\n", s);
        }
        else {
            int flag = 0;
            if (a > 1){
                for (int i = 0; i < len && a > 1; i++){
                    if (s[i] >= 'a' && s[i] <= 'z') {
                        if (b == 0) {
                            s[i] = 'A'; b++; a--;
                        }
                        else if (c == 0){
                            s[i] = '0'; c++; a--;
                        }
                    }
                    if (a && b && c) {printf("%s\n", s); flag = 1; break;}
                }
            }
            if (flag) continue;

            if (b > 1){
                for (int i = 0; i < len && b > 1; i++){
                    if (s[i] >= 'A' && s[i] <= 'Z') {
                        if (a == 0) {
                            s[i] = 'a'; a++; b--;
                        }
                        else if (c == 0){
                            s[i] = '0'; c++; b--;
                        }
                    }
                    if (a && b && c) {printf("%s\n", s); flag = 1; break;}
                }
            }
            if (flag) continue;

            if (c > 1){
                for (int i = 0; i < len && c > 1; i++){
                    if (s[i] >= '0' && s[i] <= '9') {
                        if (b == 0) {
                            s[i] = 'A'; b++; c--;
                        }
                        else if (a == 0){
                            s[i] = 'a'; a++; c--;
                        }
                    }
                    if (a && b && c) break;
                }
            }
            printf("%s\n", s);
        }
    }
    return 0;
}
```

## B

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

ll l, r;

int main(){
    scanf("%I64d%I64d", &l, &r);
    puts("YES");
    for (ll i = l; i < r; i += 2){
        printf("%I64d %I64d\n", i, i + 1);
    }
    return 0;
}
```

## C

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100 + 5;

int n, s[maxn], cnt[maxn], kind[maxn];

int main(){
    scanf("%d", &n); 
    for (int i = 0; i < n; i++){
        scanf("%d", s + i);
        cnt[s[i]]++;
    }
    int s1 = 0, s2 = 0, s3 = 0;
    for (int i = 1; i <= 100; i++){
        if (cnt[i] == 1) s1++;
        if (cnt[i] >= 3) s2++;
    }
    if (s1 % 2 == 0){
        puts("YES"); int a = s1 / 2;
        for (int i = 0; i < n; i++){
            if (cnt[s[i]] == 1) {
                if (a) putchar('A'), a--;
                else putchar('B');
            }
            else putchar('B');
        }
    }
    else {
        int a = s1 / 2, flag = 1;
        if (s2){
            puts("YES"); 
            for (int i = 0; i < n; i++){
                if (cnt[s[i]] == 1) {
                    if (a) putchar('A'), a--;
                    else putchar('B');
                }
                else if (cnt[s[i]] == 3 && flag){
                    putchar('A'); flag = 0;
                }
                else putchar('B');
            }
        }
        else puts("NO");
    }
    return 0;
}
```

# D

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <iostream>
#define ms(a,b) memset(a,b,sizeof(a))
#define lld I64d
#ifdef XLor
  #define dbg(args...) do { cout << #args << " -> "; err(args); } while (0)
#else
  #define dbg(...)
#endif
void err() { std::cout << '\n'; }
template < typename T, typename...Args >
void err(T a, Args...args){ std::cout << a << ' '; err(args...); }
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;
const ll mod = 998244353;

int n, k;
ll dp[maxn][maxn << 1][2][2] = {0};

int main(){
    scanf("%d%d", &n, &k);
    if (k == 1 || k == 2 * n){puts("2"); return 0;}
    dp[1][1][0][0] = dp[1][1][1][1] = 1;
    dp[1][2][0][1] = dp[1][2][1][0] = 1;
    for (int i = 2; i <= n; i++){
        for (int j = 1; j <= k; j++){
            (dp[i][j][0][0] += dp[i - 1][j][0][0] + dp[i - 1][j - 1][1][1] + dp[i - 1][j][0][1] + dp[i - 1][j][1][0]) %= mod;
            (dp[i][j][1][1] += dp[i - 1][j][1][1] + dp[i - 1][j - 1][0][0] + dp[i - 1][j][0][1] + dp[i - 1][j][1][0]) %= mod;
            (dp[i][j][0][1] += dp[i - 1][j - 1][0][0] + dp[i - 1][j][0][1] + dp[i - 1][j - 1][1][1]) %= mod;
            (dp[i][j][1][0] += dp[i - 1][j - 1][0][0] + dp[i - 1][j][1][0] + dp[i - 1][j - 1][1][1]) %= mod;
            if (j >= 2) (dp[i][j][0][1] += dp[i - 1][j - 2][1][0]) %= mod, (dp[i][j][1][0] += dp[i - 1][j - 2][0][1]) %= mod;
            dbg(i, j);
            dbg(dp[i][j][0][0], dp[i][j][0][1], dp[i][j][1][0], dp[i][j][1][1]);
        }
    }
    ll ans = 0;
    for (int i = 0; i < 2; i++) for (int j = 0; j < 2; j++) (ans += dp[n][k][i][j]) %= mod;
    printf("%lld", ans);
    return 0;
}
```


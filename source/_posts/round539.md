---
mathjax: true
title: Codeforces Round 539 题解
tags:
  - Codeforces
  - Solution
  - Bitmasks
  - Graph
  - Combinatorial
categories:
  - Codeforces
date: 2019-02-17 13:05:27
---

# A Sasha and His Trip

$1$ 到 $n$ 个地点排成一行，相邻两个距离为 $1$，现在要从 $1$ 不回头走到 $n$。

走一个单位距离，消耗一点油，油箱最大容量为 $v$，每个点有一个加油站，第 $i$ 点加油的费用为 $i$ 块钱每升，求最小花费。

一开始尽量加满，然后每个位置如果油箱有空余且不能支持走完全程，就加油。

写错加油的容量，过了好久才过 T^T。

# B Sasha and Magnetic Machines

给一个一个序列，选两个数 $a_i$ 和 $a_j$，选一个 $a_j$ 的因子 $x$，将两个数变成 $xa_i$ 和 $a_j \over x$，求序列总和最小能变成多少。

式子写出来

$$
\min(xa_i+{a_j \over x}-a_i-a_j)=\min((x-1)(a_i-{a_j \over x}))
$$

贪心的想，$a_i$ 肯定选最小的，$j$ 枚举即可。

# C Sasha and a Bit of Relax

给一个序列，求有多少长度为偶数的连续子序列，满足序列前一半和后一半的异或和相等。

其实这个一半是骗你的，往一半上想就凉了。

实际上，就是求长度为偶数的区间，异或和为 $0$，统计一下每种异或前缀和的个数即可。

# D Sasha and One More Name

将一个回文串，切几刀，拼成一个和一开始不同的回文串，求最小切几刀。

首先，判断出无解的情况，显然如果前一半每个字母都相同，不管怎么切都不会弄成一个不同的回文串。

对于一个回文串，他肯定存在一个前缀不是回文串，对称地切两刀，交换前后缀位置就是一个新的回文串，且与之前不同，所以刀数最多为 $2$。

所以就要判断能不能切一刀，可以发现这个串是一个回文循环串，由于 $1 \le n \le 5000$，暴力即可。

# F Sasha and Interesting Fact from Graph Theory

求满足条件的 $n$ 个点带边权的树个数，边权取值范围是 $1$ 到 $m$，满足给定了 $a$ 和 $b$，树上 $a$ 到 $b$ 路径长度恰好为 $m$。

先只考虑 $a$ 到 $b$ 的路径，我们可以枚举这个路径上的边数 $i$，然后相当于将 $m$ 划分成了 $i$ 个数，由插空法可知有 $m-1 \choose i-1$ 种情况。路径上有 $i-1$ 个点可以随意选择，方案数为 $(i-1)!{n-2 \choose i-1}$。剩下 $n-1-i$ 条边的边权可以随意选择，方案数为 $m^{n-i-1}$。最后一部分就是求剩下的点挂在这条链上的树的个数。

由 [Cayley定理](https://en.wikipedia.org/wiki/Cayley's_formula#Generalizations)，我们知道 $n$ 个带标号的点组成 $k$ 棵无根树的森林的方案数为 $k n^{n-k-1}(1 \le k \le n-1)$。

我们可以考虑将前一步选出来的路径上 $i+1$ 个点拿出来，加上剩余的点组成 $i+1$ 棵无根树。对于每棵无根树，为避免重复，将树上标号最小点设置定为根，这些根按第一部分连成树链即可。因此，只需要在最后乘上 $(i+1)n^{n-i-2}$。

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

int n, v;

int main() {
    cin >> n >> v;
    if (v >= n - 1) return printf("%d", n - 1), 0;
    int tot = v, ans = v;
    for (int i = 2; i <= n; i++) {
        tot--;
        if (tot < n - i) {
            int nd = min(n - i - tot, v - tot);
            tot += nd; ans += i * nd;
        }
    }
    cout << ans << endl;
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

int n, a[maxn];

int main() {
    scanf("%d", &n); int sum = 0;
    for (int i = 0; i < n; i++) scanf("%d", a + i), sum += a[i];
    sort(a, a + n);
    int mn = 0;
    for (int i = 1; i < n; i++) {
        for (int j = 2; j <= a[i]; j++) {
            if (a[i] % j) continue;
            mn = min(mn, (j - 1) * (a[0] - a[i] / j));
            // cout << (a[0] - a[i] / j) << endl;
        }

    }
    cout << sum + mn << endl;
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
#include <map>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 300000 + 5;

int n, a[maxn], pre[maxn];
map<int,int> mp[2];

int main() {
    scanf("%d", &n); mp[0][0]++;
    for (int i = 1; i <= n; i++) {
        scanf("%d", a + i);
        pre[i] = pre[i - 1] ^ a[i];
        mp[i % 2][pre[i]]++;
    }
    // for (int i = 1; i <= n; i++) cout << pre[i] << ' '; cout << endl;
    ll ans = 0;
    for (auto& x: mp[0]) {
        ans += 1ll * x.second * (x.second - 1) / 2;
    }
    for (auto& x: mp[1]) {
        ans += 1ll * x.second * (x.second - 1) / 2;
    }
    cout << ans << endl;
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
#include <string>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int maxn = 100000 + 5;

string s;
bool check(string s) { 
    for (int i = 0; i < s.length(); i++) if (s[i] != s[s.length() - 1 - i]) return 0;
    return 1;
}

int main() {
    ios::sync_with_stdio(false);
    cin >> s;
    int n = s.length(), flag = 1;
    for (int i = 1; i < n / 2; i++) if (s[i] != s[i - 1]) {
        flag = 0; break;
    }
    if (flag) return puts("Impossible"), 0;
    for (int i = 1; i < n - 1; i++) {
        string t = s.substr(i) + s.substr(0, i);
        if (s == t) continue;
        if (check(t)) return puts("1"), 0;
    }
    puts("2");
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 1e9 + 7;
const int maxn = 1000000 + 5;

int f[maxn], inv[maxn], finv[maxn];
void init(){
    inv[1] = 1;
    for (int i = 2; i < maxn; i++) inv[i] = (mod - mod / i) * 1ll * inv[mod % i] % mod;
    f[0] = finv[0] = 1; 
    for (int i = 1; i < maxn; i++) {
        f[i] = f[i - 1] * 1ll * i % mod;
        finv[i] = finv[i - 1] * 1ll * inv[i] % mod;
    }
}
int C(int n, int m){
    if (m < 0 || m > n) return 0;
    return f[n] * 1ll * finv[n - m] % mod * finv[m] % mod;
}
ll qpow(ll x, ll n) {
    ll r = 1;
    while (n > 0) {
        if (n & 1) r = r * x % mod;
        n >>= 1; x = x * x % mod;
    }
    return r;
}
ll T(ll n, ll k) {
    if (k == n) return 1;
    return k * qpow(n, n - k - 1) % mod;
}

int n, m, a, b;

int main() {
    init();
    cin >> n >> m >> a >> b;
    ll ans = 0;
    for (int i = 1; i < n; i++) {
        ll tot = 1ll * C(m - 1, i - 1) * C(n - 2, i - 1) % mod * f[i - 1] % mod * qpow(m, n - i - 1) % mod * T(n, i + 1) % mod;
        ans += tot; if (ans >= mod) ans -= mod;
    }
    cout << ans << endl;
    return 0;
}
```
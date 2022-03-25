---
mathjax: true
title: Codeforces Round 558 题解
tags:
  - Codeforces
  - Solution
  - String
  - KMP
  - Geometry
categories:
  - Codeforces
date: 2019-05-10 12:35:11
---

# A Eating Soup

# B Cat Party

给定序列 $a$，求一个最长前缀，满足删除一个数后，剩下每种数的出现次数相同。

维护每种数的出现次数，每种出现次数的出现次数。

显然，有答案时，出现次数的种类最多为 $2$，分类讨论判断一下即可。

# C Power Transmission

给定 $1000$ 个点，两两连着直线，求有多少直线相交，重合的直线只算一次。

只需要将直线去重即可，对于每种斜率，统计即可。

把直线转化为一般方程去重即可。

# D Mysterious Code

有一个未知串 $p$，两个模式串 $s$ 和 $t$，要求最大化 $p$ 串中 $s$ 串出现次数减去 $t$ 串出现次数。

对 $s$ 和 $t$ 串建出 $fail$ 指针，记 $dp[t][i][j]$ 表示 $p$ 串前 $t$ 个匹配到两个串 $i$ 和 $j$ 位置时的最大值。对于每个新字符，如果是通配符就跑所有字母的转移，转移就是在两个 $fail$ 指针上找到下一个位置，并更新计数值。

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
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, m, vis[maxn];

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n && m; i += 2, m--) vis[i] = 1;
    for (int i = 1; i <= n && m; i++) {
        if (!vis[i]) {
            vis[i] = 1; m--;
        }
    }
    int ans = 0;
    for (int i = 2, j; i <= n; i = j) {
        if (vis[i]) {
            j = i + 1; continue;
        }
        ans++;
        j = i; while (j <= n && !vis[j]) j++;
    }
    cout << ans;
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
#include <map>
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
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, a[maxn], bag[maxn], cnt[maxn], sz = 0;

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    int ans = 1; map<int,int> mp;
    for (int i = 1; i <= n; i++) {
        if (bag[a[i]]) {
            mp[bag[a[i]]]--;
            if (mp[bag[a[i]]] == 0) sz--, mp.erase(bag[a[i]]);
        }
        bag[a[i]]++;
        mp[bag[a[i]]]++;
        if (mp[bag[a[i]]] == 1) sz++;
        if (sz == 1) {
            if (mp.begin()->first == 1) ans = i;
            if (mp.begin()->second == 1) ans = i;
        } else if (sz == 2) {
            auto it = mp.begin(), it2 = mp.begin(); it2++;
            if (it->first - 1 == it2->first || it->first == 1) {
                if (it->second == 1) ans = i;
            }
            if (it2->first - 1 == it->first || it2->first == 1) {
                if (it2->second == 1) ans = i;
            }
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
#include <cmath>
#include <algorithm>
#include <vector>
#include <utility>
#include <set>
#include <map>
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
typedef pair<ll,ll> PLL;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

const double eps = 1e-5;
const double pi = acos(-1.0);

int gcd(int a, int b) { return b ? gcd(b, a % b) : a; }
ll gcd(ll a, ll b) { return b ? gcd(b, a % b) : a; }

inline int dcmp(double x) { 
    // double大小比较，考虑精度eps
    if (fabs(x) < eps) return 0;
    else return x < 0 ? -1 : 1;
}
inline int zero(double x) {
    return fabs(x) < eps;
}

struct Point {
    int x, y;
    Point(int x = 0, int y = 0): x(x), y(y){}
    bool operator<(const Point& b) const {
        return x < b.x;
    }
    bool operator==(const Point& b) const {
        return x == b.x && y == b.y;
    }
};
typedef Point Vector;

Vector operator + (Vector a, Vector b) { return Vector(a.x + b.x, a.y + b.y); }
Vector operator - (Point a, Point b) { return Vector(a.x - b.x, a.y - b.y); }

int cross(Vector a, Vector b) { return a.x * b.y - a.y * b.x; }
ll length(Vector a) { return 1ll * a.x * a.x + 1ll * a.y * a.y; }
int xmult(Point a, Point b, Point c) {
    // 0 -> 三点共线
    // + -> AC 在 AB 的逆时针方向
    // - -> AC 在 AB 的顺时针方向 
    return cross(b - a, c - a);
}

struct Line {
    Point p; Vector v;
    Line(Point a, Point b) : p(a), v(b - a) {}
};

PLL getDistanceToLine(Point p, Point a, Point b) {
    Vector v1 = b - a, v2 = p - a;
    ll x = 1ll * cross(v1, v2) * cross(v1, v2);
    ll y = length(v1);
    if (x == 0) return { 0, 1 };
    else if (x > 0) {
        ll g = gcd(x, y);
        return { x / g, y / g };
    } else {
        ll g = gcd(-x, y);
        return { -x / g, y / g };
    }
}

int n;
Point a[1005];
vector<Line> b, c;

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d%d", &a[i].x, &a[i].y);
    for (int i = 1; i <= n; i++) {
        for (int j = i + 1; j <= n; j++) {
            b.push_back({a[i], a[j]});
        }
    }
    map< PII, vector<PII> > mp;
    for (auto& x: b) {
        if (x.v.x == 0) {
            mp[{0, 1}].push_back({x.p.x, x.p.y});
        } else if (x.v.y == 0) {
            mp[{1, 0}].push_back({x.p.x, x.p.y});
        } else {
            int g = gcd(abs(x.v.x), abs(x.v.y));
            if (1ll * x.v.x * x.v.y > 0) {
                mp[{abs(x.v.x) / g, abs(x.v.y) / g}].push_back({x.p.x, x.p.y});
            } else {
                mp[{-abs(x.v.x) / g, abs(x.v.y) / g}].push_back({x.p.x, x.p.y});
            }
        }
    }
    ll ans = 0; int sum = 0;
    map<PII,int> cnt;
    for (auto& v: mp) {
        dbg(v.first.first, v.first.second);
        // vector<PII> vec;
        set<PLL> st;
        PII ed = v.second[0];
        Point a(v.second[0].first, v.second[0].second);
        Point b(v.first.first, v.first.second);
        b = a + b;
        for (int i = 0; i < (int)v.second.size(); i++) {
            Point p(v.second[i].first, v.second[i].second);
            PLL h = getDistanceToLine(p, a, b);
            if (xmult(p, a, b) < 0) h.first = -h.first;
            dbg(h.first, h.second, xmult(p, a, b));
            st.insert(h);
        }
        // dbg(st.size());
        // set<ll> st;
        // for (PII x: v.second) {
        //     st.insert(1ll * v.first.second * x.first - 1ll * v.first.first * x.second);
        // }
        sum += (int)st.size();
        cnt[v.first] = (int)st.size();
        dbg(st.size());
    }
    for (auto& x: cnt) {
        ans += 1ll * x.second * (sum - x.second);
    }
    cout << ans / 2;
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
const int inf = 1 << 30;
const int maxn = 1000 + 5;
const int maxm = 50 + 5;

int sn, tn, pn;
char s[maxm], t[maxm], p[maxn]; 

int fail1[maxm], fail2[maxm], nxt1[maxm][26], nxt2[maxm][26];
void getfail(int len, char* s, int fail[], int nxt[][26]) {
    // int i = 1, k = 0;
    // while (i <= len) {
    //     if (!k || s[k] == s[i]) i++, k++, fail[i] = k;
    //     else k = fail[k];
    // }
    fail[1] = 0;
    for (int i = 2; i <= len; i++) {
        int cur = fail[i - 1];
        while (cur > 0 && s[cur + 1] != s[i])
            cur = fail[cur];
        if (s[cur + 1] == s[i])
            ++cur;
        fail[i] = cur;
    }

    for (int i = 0; i <= len; i++) {
        dbg(i, fail[i]);
        for (int ch = 0; ch < 26; ch++) {
            int tot = i;
            while (tot && s[tot + 1] != ch + 'a') tot = fail[tot];
            if (s[tot + 1] == ch + 'a') tot++;
            nxt[i][ch] = tot;
            dbg(i, ch, nxt[i][ch]);
        }
    }
}

int dp[maxn][maxm][maxm];
void update(int f[][maxm], int g[][maxm], char ch) {
    for (int i = 0; i <= sn; i++) {
        for (int j = 0; j <= tn; j++) {
            // if (g[i][j] == -inf) continue;
            // dbg(i, j, g[i][j]);
            for (int c = 0; c < 26; c++) {
                if (ch != '*' && c + 'a' != ch) continue;
                int nxs = nxt1[i][c], nxt = nxt2[j][c];
                f[nxs][nxt] = max(f[nxs][nxt], g[i][j] + (nxs == sn) - (nxt == tn));
                // dbg(nxs, nxt, f[nxs][nxt]);
            }
        }
    }
}

int main() {
    scanf("%s%s%s", p + 1, s + 1, t + 1);
    pn = strlen(p + 1); sn = strlen(s + 1); tn = strlen(t + 1);
    getfail(sn, s, fail1, nxt1);
    getfail(tn, t, fail2, nxt2);
    for (int i = 0; i <= pn; i++) for (int j = 0; j <= sn; j++) for (int k = 0; k <= tn; k++) dp[i][j][k] = -inf;
    dp[0][0][0] = 0;
    for (int i = 1; i <= pn; i++) {
        // dbg(i);
        update(dp[i], dp[i - 1], p[i]);
    }
    int ans = -inf;
    for (int i = 0; i <= sn; i++) for (int j = 0; j <= tn; j++) ans = max(ans, dp[pn][i][j]);
    cout << ans;
    return 0;
}
```
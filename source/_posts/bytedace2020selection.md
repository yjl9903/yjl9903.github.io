---
mathjax: true
title: 2020 ByteDance 网络选拔赛
tags:
  - Trainings
categories:
  - 训练
date: 2019-12-21 22:39:05
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  36  |   8    |  O  |  .  |  O  |  O  |  O  |  O  |  .  |  .  |  .  |  .  |  O  |  O  |  O  |

[榜单](http://opentrains.mipt.ru/~ejudge/showres.cgi?data=bytedance2020online)

<!--more-->

# A

Solved by XLor.

观察：操作带有最大值的前缀不优。

考虑最大值的第一次出现的前一个位置，就是将这个前缀最大值抬到总体最大值，这样反复操作。

也就是说每次往前缀后插入一个数，这个数必须大于等于最大值才有用，得到一个类似阶梯的形状。

枚举这个前缀后缀，使得他变成最大值的花费是与最大值的高度差，产生的最大值是一个后缀的个数。

询问点总和是常数，差分一下变成前缀，即每个有效点是 $(h,i)$，$h$ 表示高度，$i$ 表示第几个关键点。

询问等价于 $hx-iy$，是一个点积的形式。维护 $(h,i)$ 向量的上凸壳，询问时在凸壳上三分。

# C

Solved by Forsaken and XLor.

圆只有 $50$ 个，每个圆拆出 $10000$ 个点，跑最小圆覆盖即可。

# D

Solved by Forsaken.

# E

Solved by XLor.

记 $dp(i)$ 表示前缀 $i$ 的划分方案数。

枚举右端点，快速得出哪些左端点是合法的。

考虑每种数，左端点在该数倒数第二次和最后一次出现的这个左开右闭区间内，合法。

维护一下每种数的出现次数，移动右端点时，更新一下区间覆盖。

答案就是每个覆盖点的左边一个 $dp$ 值之和。

# F

Solved by miaojie, Forsaken and XLor.

论文关键词：sum free subset。

以下为 ac 乱搞。

排序后，枚举 $n/3$ 长度的连续段，判断最大值和最小值间的关系。

得到的数组看起来会比较均匀，不会有部分密集的值域。

然后，模 $2,3,4,5$ 讨论一下。

# K

Solved by miaojie and XLor.

先将所有有效数字归并在一起，然后就是逆序对个数。

# L

Solved by Forsaken.

答案要么是循环节这个前缀，要么是加一。

# M

Solved by XLor.

排序后，枚举第二个数组的循环移位。

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<ll,ll> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

int n, cnt, a[maxn];

PII operator - (PII a, PII b) {
  return make_pair(a.first - b.first, a.second - b.second);
}
ll cross(PII a, PII b) {
  return a.first * b.second - a.second * b.first;
}
ll xmult(PII a, PII b, PII c) {
  return cross(b - a, c - a);
}

vector<PII> stk;
bool check(PII x) {
  if ((int)stk.size() <= 2) return false;
  return xmult(stk[(int)stk.size() - 2], stk.back(), x) > 0;
}
void push(int x, int y) {
  PII v(x, y);
  while (check(v)) {
    stk.pop_back();
  }
  stk.push_back(v);
}
ll query(int x, int y) {
  assert(!stk.empty());
  ll h = stk.front().second, i = stk.front().first;
  ll ans = 1ll * x * h - 1ll * y * i;
  for (int l = 0, r = (int)stk.size() - 1; l <= r;) {
    int len = (r - l) / 3, m1 = l + len, m2 = r - len;
    ll s1 = (ll)x * stk[m1].second - (ll)y * stk[m1].first;
    ll s2 = (ll)x * stk[m2].second - (ll)y * stk[m2].first;
    if (s1 > s2) {
      r = m2 - 1;
      if (ans < s1) ans = s1;
    } else {
      l = m1 + 1;
      if (ans < s2) ans = s2;
    }
  }
  return ans - 1ll * x * a[cnt] + 1ll * y * (cnt + 1);
}

int main() {
  scanf("%d", &n);
  for (int i = 1, b, x, y; i <= n; i++) {
    scanf("%d%d%d", &b, &x, &y);
    if (b >= a[cnt]) {
      a[++cnt] = b; // b, cnt
      push(cnt, b);
    }
    printf("%lld\n", query(x, y));
  }
  return 0;
}
```

## C

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#include <random>
#include <ctime>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;
const double eps = 1e-6;
const double pi = acos(-1.0);

inline double sqr(double x) {
  return x * x;
}

struct Point {
  double x, y;
  Point(double x = 0, double y = 0): x(x), y(y) {}
};

mt19937 rnd(time(0));

double getd(Point a,Point b) { return sqrt(sqr(a.x-b.x)+sqr(a.y-b.y));}
Point getO(Point p1,Point p2,Point p3) {
  Point res;
  double a=p2.x-p1.x,b=p2.y-p1.y,c=p3.x-p2.x,d=p3.y-p2.y;
  double e=sqr(p2.x)+sqr(p2.y)-sqr(p1.x)-sqr(p1.y);
  double f=sqr(p3.x)+sqr(p3.y)-sqr(p2.x)-sqr(p2.y);
  res.x=(f*b-e*d)/(c*b-a*d)/2.0; 
  res.y=(a*f-e*c)/(a*d-b*c)/2.0;
  return res;
}

double mincir(vector<Point> p) {
  shuffle(begin(p), end(p), rnd);
  Point O = p[1]; 
  double R = 0;
  int n = (int)p.size();
  for(int i = 0; i < n; i++)
  if(getd(p[i],O)-R>eps) {
    O=p[i]; R=0;
    for(int j=1;j<i;j++)
    if(getd(p[j],O)-R>eps) {
      O=(Point){(p[i].x+p[j].x)/2.0,(p[i].y+p[j].y)/2.0};
      R=getd(p[i],p[j])/2.0;

      for(int k=1;k<j;++k)
        if(getd(p[k],O)-R>eps) {
          O=getO(p[i],p[j],p[k]);
          R=getd(p[i],O);
        }
    }
  }
  return R;
} 

const int N = 10000;

int main() {
  int n; scanf("%d", &n);
  vector<Point> v;
  for (int i = 1; i <= n; i++) {
    int x, y, r; scanf("%d%d%d", &x, &y, &r);
    for (int j = 0; j < N; j++) {
      double t = j * 2.0 * pi / N;
      double xx = x + r * cos(t);
      double yy = y + r * sin(t);
      v.push_back((Point){xx, yy});
    }
  }
  printf("%.4lf\n", mincir(v));
  return 0;
}
```

## D

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int max_n=300005;
int n,a[max_n];
int S[max_n],tail;
int main(){
    scanf("%d",&n);
    for(int i=1;i<=n;i++)scanf("%d",a+i);
    ll ans=0;
    for(int i=n;i>=1;i--){
        if(tail){
            if(S[tail]%2==a[i]%2){
                ans+=(S[tail]+a[i])/2;
                tail--;
            }
            else{
                ans+=(a[i]-1-S[tail])/2;
                a[i]=S[tail]+1;
                S[++tail]=a[i];
            }
        }
        else{
            ans+=a[i]/2;
            if(a[i]&1)S[++tail]=1;
        }
    }
    if(tail&1)ans+=(1ll*tail*(tail+1)/2-(tail+1)/2)/2;
    else ans+=(1ll*tail*(tail+1)/2-tail/2)/2;
    printf("%lld\n",ans);
    return 0;
}
```

## E

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#ifdef XLor
  #define dbg(args...) cout << "\033[32;1m" << #args << " -> ", err(args)
  void err() { std::cout << "\033[39;0m" << std::endl; }
  template<typename T, typename...Args>
  void err(T a, Args...args) { std::cout << a << ' '; err(args...); }
#else
  #define dbg(...)
#endif
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

inline int add(int x, int y) {
  x += y;
  return x >= mod ? x -= mod : x;
}
inline int sub(int x, int y) {
  x -= y;
  return x < 0 ? x += mod : x;
}
inline int mul(int x, int y) {
  return 1ll * x * y % mod;
}
inline int qpow(int x, ll n) {
  int r = 1;
  while (n > 0) {
    if (n & 1) r = 1ll * r * x % mod;
    n >>= 1; x = 1ll * x * x % mod;
  }
  return r;
}
inline int inv(int x) {
  return qpow(x, mod - 2);
}

int n, a[maxn], dp[maxn], pre[maxn];
vector<int> bag[maxn];

int cnt[maxn << 2], len[maxn << 2];
void upd(int rt, int l, int r) {
  if (cnt[rt]) {
    len[rt] = pre[r - 1];
    if (l >= 2) len[rt] = sub(len[rt], pre[l - 2]);
  } else if (l < r) {
    len[rt] = add(len[rt << 1], len[rt << 1 | 1]);
  } else {
    len[rt] = 0;
  }
}
void update(int L, int R, int x, int l = 1, int r = n, int rt = 1) {
  if (L <= l && r <= R) {
    cnt[rt] += x; upd(rt, l, r);
    return ;
  }
  int m = (l + r) / 2;
  if (L <= m) update(L, R, x, lson);
  if (R > m) update(L, R, x, rson);
  upd(rt, l, r);
}

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
    bag[i].push_back(0);
  }
  pre[0] = dp[0] = 1;
  for (int i = 1; i <= n; i++) {
    update(bag[a[i]].back() + 1, i, 1);
    bag[a[i]].push_back(i);
    int pos = (int)bag[a[i]].size() - 3;
    if (pos >= 0) {
      update(bag[a[i]][pos] + 1, bag[a[i]][pos + 1], -1);
    }
    dp[i] = len[1];
    pre[i] = add(dp[i], pre[i - 1]);
  }
  printf("%d\n", dp[n]);
  return 0;
}
```

## F

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 300000 + 5;

int n, a[maxn];
vector<int> bag[10];

int out(const vector<int>& v) {
  printf("%d\n", (int)v.size());
  for (auto x: v) printf("%d ", x);
  return 0;
}

int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
  }

  sort(a + 1, a + 1 + n, greater<int>());
  for (int i = 1; i + n / 3 - 1 <= n; i++) {
    if (a[i] < a[i + n / 3 - 1] * 2) {
      printf("%d\n", n / 3);
      for (int j = 0; j < n / 3; j++) {
        printf("%d ", a[i + j]);
      }
      return 0;
    }
  }
  
  for (int i = 1; i <= n; i++) bag[a[i] % 2].push_back(a[i]);
  if ((int)bag[1].size() >= n / 3) {
    return out(bag[1]);
  }

  bag[0].clear(); bag[1].clear();
  for (int i = 1; i <= n; i++) bag[a[i] % 3].push_back(a[i]);
  if ((int)bag[1].size() >= n / 3) {
    return out(bag[1]);
  } else if ((int)bag[2].size() >= n / 3) {
    return out(bag[2]);
  }

  bag[0].clear(); bag[1].clear(); bag[2].clear();
  for (int i = 1; i <= n; i++) bag[a[i] % 4].push_back(a[i]);
  if ((int)bag[1].size() + (int)bag[3].size() >= n / 3) {
    vector<int> v(bag[1]);
    for (auto x: bag[3]) v.push_back(x);
    return out(v);
  } else if ((int)bag[2].size() >= n / 3) {
    return out(bag[2]);
  }

  bag[0].clear(); bag[1].clear(); bag[2].clear(); bag[3].clear();
  for (int i = 1; i <= n; i++) bag[a[i] % 5].push_back(a[i]);
  if ((int)bag[1].size() + (int)bag[4].size() >= n / 3) {
    vector<int> v(bag[1]);
    for (auto x: bag[4]) v.push_back(x);
    return out(v);
  } else if ((int)bag[2].size() + (int)bag[3].size() >= n / 3) {
    vector<int> v(bag[2]);
    for (auto x: bag[3]) v.push_back(x);
    return out(v);
  }

  assert(0);
  return 0;
}
```

## K

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 200000 + 5;

int n, k, a[maxn], b[maxn];

void update(int i, int x) {
  for (; i <= n; i += i & -i) b[i] += x;
}
int query(int i) {
  int r = 0;
  for (; i; i -= i & -i) r += b[i];
  return r;
}

ll pre[maxn], suf[maxn];

int main() {
  scanf("%d%d", &n, &k);
  vector<int> p;
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
    if (a[i] <= k) p.push_back(i);
  }

  int cnt = 0;
  for (int i = 1; i <= n; i++) {
    pre[i] = pre[i - 1];
    if (a[i] <= k) {
      cnt++;
    } else {
      pre[i] += cnt;
    }
  }
  cnt = 0;
  ll ans = pre[n];
  for (int i = n; i >= 1; i--) {
    suf[i] = suf[i + 1];
    if (a[i] <= k) {
      cnt++;
    } else {
      suf[i] += cnt;
    }
    ans = min(ans, suf[i] + pre[i - 1]);
  }

  ll sum = 0;
  for (int i = k - 1; i >= 0; i--) {
    sum += query(a[p[i]]);
    update(a[p[i]], 1);
  }
  printf("%lld\n", sum + ans);
  return 0;
}
```

## L

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int max_n=200005;
int n,k;
char s[max_n];
int a[max_n];
int b[max_n];
int main(){
    scanf("%d%d",&n,&k);
    scanf(" %s",s);
    for(int i=0;i<n;i++)a[i]=s[i]-'0';
    for(int i=0;i<k;i++)b[i]=a[i];
    bool f=true;
    for(int i=k;i<n;i++){
        b[i]=a[i%k];
        if(b[i]==a[i])continue;
        else{
            if(b[i]<a[i]){
                f=false;
            }
            break;
        }
    }
    if(f==true){
        for(int i=0;i<n;i++)b[i]=a[i%k];
        printf("%d\n",n);
        for(int i=0;i<n;i++)printf("%d",b[i]);
        printf("\n");
    }
    else{
        for(int i=0;i<k;i++)b[i]=a[i];
        b[k-1]++;
        for(int i=k-1;i>=1;i--){
            b[i-1]+=b[i]/10;
            b[i]%=10;
        }
        if(b[0]!=10){
            printf("%d\n",n);
            for(int i=k;i<n;i++)b[i]=b[i%k];
            for(int i=0;i<n;i++)printf("%d",b[i]);
            printf("\n");
        }
        else{
            b[0]=1;
            for(int i=1;i<k;i++)b[i]=0;
            for(int i=k;i<=n;i++)b[i]=b[i%k];
            printf("%d\n",n+1);
            for(int i=0;i<=n;i++)printf("%d",b[i]);
            printf("\n");
        }
    }
    return 0;
}
```

## M

```c++
#include <iostream>
#include <cstdio>
#include <cassert>
#include <cstring>
#include <cmath>
#include <functional>
#include <algorithm>
#include <utility>
#include <vector>
#include <string>
#include <map>
#include <set>
#ifdef XLor
  #define dbg(args...) cout << "\033[32;1m" << #args << " -> ", err(args)
  void err() { std::cout << "\033[39;0m" << std::endl; }
  template<typename T, typename...Args>
  void err(T a, Args...args) { std::cout << a << ' '; err(args...); }
#else
  #define dbg(...)
#endif
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, m, a[maxn], b[maxn];

int main() {
  scanf("%d%d", &n, &m);
  for (int i = 1; i <= n; i++) {
    scanf("%d", a + i);
  }
  for (int i = 1; i <= n; i++) {
    scanf("%d", b + i);
  }
  auto sub = [&](int x, int y) {
    x -= y; return x >= 0 ? x : x + m;
  };
  sort(a + 1, a + 1 + n);
  sort(b + 1, b + 1 + n);
  int ans = m + 1;
  for (int i = 0; i < n; i++) {
    int d = sub(b[i + 1], a[1]);
    int flag = 1;
    dbg(i);
    for (int j = 2; j <= n; j++) {
      dbg(j, (i + j - 1) % n + 1);
      if (sub(b[(i + j - 1) % n + 1], a[j]) != d) {
        flag = 0; break;
      }
    }
    if (flag) {
      ans = min(ans, d);
    }
  }
  printf("%d\n", ans);
  return 0;
}
```

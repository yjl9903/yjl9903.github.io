---
mathjax: true
title: 2019 南昌邀请赛网络赛
tags:
  - Trainings
  - Math
  - Data Structure
  - RMQ
  - Tree
  - String
categories:
  - 训练
date: 2019-04-20 17:01:34
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  74  |    7   |  O  |  .  |  .  |  !  |  .  |  .  |  O  |  O  |  O  |  O  |  O  |  .  |  O  |

<!--more-->

# A PERFECT NUMBER PROBLEM

Solved by wb.

# G tsy's number

Solved by wb.

筛。

# H Coloring Game

Solved by wb.

# I Max answer

Solved by XLor.

枚举最小值位置，单调栈预处理前一个比这个位置小的数和下一个比这个位置小的数，ST 表询问前缀和的最值差分。

# J Distance on the tree

Solved by XLor.

dfs 建主席树，LCA 差分

# K MORE XOR

Solved by XLor.

找规律。

# M Subsequence

Solved by XLor.

序列自动机。

# 代码

## A

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
int a[5]={6,28,496,8128,33550336};
int main()
{
    for(int i=0;i<5;i++)printf("%d\n",a[i]);
    return 0;
}
```

## G

```c++
#pragma GCC optimize(3,"Ofast","inline")
#include<bits/stdc++.h>
#define forsaken
#ifdef forsaken
  #define dbg(args...) do {cout << #args << " : "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
#define IOS ios::sync_with_stdio(false),cin.tie(0),cout.tie(0)
#define ms(a,b) memset(a,b,sizeof(a))
#define msn(a,n,b) for(int i=0;i<=n;i++)a[i]=b
#define lson l,mid,rt<<1
#define rson mid+1,r,rt<<1|1
#define fi first
#define se second
using namespace std;
mt19937 rng_32(chrono::steady_clock::now().time_since_epoch().count());
typedef long long ll;
typedef long double ld;
typedef pair<int,int> P;
//#define int unsigned int
const int mod=(1<<30);
const int seed=233;
const double PI=acos(-1.0);
const int inf=0x3f3f3f3f;
const int max_n=10000005;
namespace {
    inline int Add(int x,int y){return (x+=y)>=mod?x-mod:x;}
	inline int Sub(int x,int y){return (x-=y)<0?x+mod:x;}
    inline int Mul(int x,int y) {return 1ll*x*y%mod;}
    inline int Pow(int x,int y=mod-2){int res=1;while(y){if(y&1)res=1ll*res*x%mod;x=1ll*x*x%mod;y>>=1;}return res;}
}
/**********************head************************/
int T;
int n;
bool p[max_n];
int cnt;
int pri[max_n];
int f[max_n];
int divisor[max_n];
ll s(ll n,int f)
{
    if(f==0)return n;
    else if(f==1)return 1ll*n*(n+1)/2%mod;
    else
    {
        ll s1=n*(n+1)/2;
        if(s1%3==0)s1=s1/3%mod*(2*n+1)%mod;
        else s1=s1%mod*((2*n+1)/3)%mod;
        return s1;
    }
}
void sieve(int n)
{
    f[1]=1;
    p[0]=p[1]=true;
    for(int i=2;i<=n;i++)
    {
        if(!p[i])pri[++cnt]=i,divisor[i]=i,f[i]=i-2;
        for(int j=1;j<=cnt&&1ll*i*pri[j]<=n;j++)
        {
            p[i*pri[j]]=true;
            if(i%pri[j])
            {
                divisor[i*pri[j]]=pri[j];
                f[i*pri[j]]=f[i]*f[pri[j]];
            }
            else
            {
                divisor[i*pri[j]]=divisor[i]*pri[j];
                if(i==divisor[i])
                {
                    if(!p[i])f[i*pri[j]]=f[i]*i+1;
                    else f[i*pri[j]]=f[i]*pri[j];
                }
                else
                {
                    f[i*pri[j]]=f[i/divisor[i]]*f[divisor[i*pri[j]]];
                }
                break;
            }
        }
    }
    for(int i=1;i<=n;i++)f[i]=Mul(f[i],Pow(i,3));
    for(int i=1;i<=n;i++)f[i]=Add(f[i-1],f[i]);
}
int w(int n)
{
    return n*n*n;
}
int solve(int n)
{
    int res=0;
    for(int l=1,r;l<=n;l=r+1)
    {
        r=n/(n/l);
        int tmp=1;
        for(int j=0;j<3;j++)tmp=Mul(tmp,s(n/l,j));
        int st=Sub(f[r],f[l-1]);
        res=Add(res,Mul(tmp,st));
    }
    return res;
}
signed main()
{
    cin>>T;
    sieve(max_n-1);
    while(T--)
    {
        cin>>n;
        printf("%d\n",solve(n));
    }
    return 0;
}
```

## H

```c++
#pragma GCC optimize(3,"Ofast","inline")
#include<bits/stdc++.h>
#define forsaken
#ifdef forsaken
  #define dbg(args...) do {cout << #args << " : "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
#define IOS ios::sync_with_stdio(false),cin.tie(0),cout.tie(0)
#define ms(a,b) memset(a,b,sizeof(a))
#define msn(a,n,b) for(int i=0;i<=n;i++)a[i]=b
#define lson l,mid,rt<<1
#define rson mid+1,r,rt<<1|1
#define fi first
#define se second
using namespace std;
mt19937 rng_32(chrono::steady_clock::now().time_since_epoch().count());
typedef long long ll;
typedef long double ld;
typedef pair<int,int> P;
const int mod=1e9+7;
const int seed=233;
const double PI=acos(-1.0);
const int inf=0x3f3f3f3f;
const int max_n=100005;
namespace {
    inline int Add(int x,int y){return (x+=y)>=mod?x-mod:x;}
	inline int Sub(int x,int y){return (x-=y)<0?x+mod:x;}
    inline int Mul(int x,int y) {return 1ll*x*y%mod;}
    inline int Pow(int x,int y=mod-2){int res=1;while(y){if(y&1)res=1ll*res*x%mod;x=1ll*x*x%mod;y>>=1;}return res;}
}
/**********************head************************/
int n;
int main()
{
    scanf("%d",&n);
    int ans;
    if(n==1)ans=1;
    else ans=Mul(4,Pow(3,n-2));
    printf("%d\n",ans);
    return 0;
}
```

## I 

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
const int maxn = 500000 + 5;

int n, a[maxn], nxt[maxn][2];
ll pre[maxn];

ll mx[maxn][20], mn[maxn][20];
void init() {
    for (int i = 0; i <= n; i++) mx[i][0] = mn[i][0] = pre[i];
    for (int j = 1; j < 20; j++)
        for (int i = 0; i + (1 << j) <= n + 1; i++) {
            mx[i][j] = max(mx[i][j - 1], mx[i + (1 << (j - 1))][j - 1]);
            mn[i][j] = min(mn[i][j - 1], mn[i + (1 << (j - 1))][j - 1]);
        }
}
ll qmax(int l, int r) {
    int k = 0; while ((1 << (k + 1)) <= r - l + 1) k++;
    return max(mx[l][k], mx[r - (1 << k) + 1][k]);
}
ll qmin(int l, int r) {
    int k = 0; while ((1 << (k + 1)) <= r - l + 1) k++;
    return min(mn[l][k], mn[r - (1 << k) + 1][k]);
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i), pre[i] = a[i] + pre[i - 1];
    init();
    vector<int> st;
    for (int i = 1; i <= n; i++) {
        while (!st.empty() && a[st.back()] > a[i]) {
            nxt[st.back()][0] = i - 1; st.pop_back();
        }
        st.push_back(i);
    }
    for (int i = 1; i <= n; i++) if (!nxt[i][0]) nxt[i][0] = n;
    st.clear();
    for (int i = n; i >= 1; i--) {
        while (!st.empty() && a[st.back()] > a[i]) {
            nxt[st.back()][1] = i + 1; st.pop_back();
        }
        st.push_back(i);
    }
    for (int i = 1; i <= n; i++) if (!nxt[i][1]) nxt[i][1] = 1;

    for (int i = 1; i <= n; i++) {
        dbg(i,nxt[i][0], nxt[i][1]);
    }

    ll ans = 0;
    for (int i = 1; i <= n; i++) {
        int m = i, l = nxt[i][1], r = nxt[i][0];
        ll mx = qmax(m, r);
        ll mn = qmin(l - 1, m - 1);
        ans = max(ans, 1ll * a[i] * (mx - mn));
        mx = qmin(m, r);
        mn = qmax(l - 1, m - 1);
        ans = max(ans, 1ll * a[i] * (mx - mn));
    }
    cout << ans << endl;
    return 0;
}
```

## J

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
#define lson l, m, ls[rt]
#define rson m + 1, r, rs[rt]
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, q, nn;
vector<int> lsh;
vector<PII> edge[maxn];

namespace hld {
    int siz[maxn], dep[maxn], fa[maxn], son[maxn], top[maxn];
    void dfs(int u, int f) {
        dep[u] = dep[f] + 1; fa[u] = f; siz[u] = 1; son[u] = 0;
        int m = -1;
        for (auto& x: edge[u]) {
            int v = x.first;
            if (v == f) continue;
            dfs(v, u);
            siz[u] += siz[v];
            if (siz[v] > m) son[u] = v, m = siz[v];
        }
    }
    void dfs(int u, int f, int tp) {
        top[u] = tp;
        if (!son[u]) return;
        dfs(son[u], u, tp); // !
        for (auto& x: edge[u]) {
            int v = x.first;
            if (v == f || v == son[u]) continue; // !
            dfs(v, u, v);
        }
    }
    void init() {
        dfs(1, 0); dfs(1, 0, 1);
    }
    int qlca(int u, int v) {
        while (top[u] != top[v]){
            if (dep[top[u]] < dep[top[v]]) swap(u, v);
            u = fa[top[u]];
        }
        return dep[u] < dep[v] ? u : v;
    }
}

int tot, ls[maxn * 40], rs[maxn * 40], cnt[maxn * 40], root[maxn];
void update(int i, int pre, int l, int r, int& rt) {
    rt = ++tot; ls[rt] = ls[pre]; rs[rt] = rs[pre]; cnt[rt] = cnt[pre] + 1;
    if (l == r) return ;
    int m = (l + r) / 2;
    if (i <= m) update(i, ls[pre], lson);
    else update(i, rs[pre], rson);
}
int query(int i, int l, int r, int rt) {
    if (r <= i) return cnt[rt];
    int m = (l + r) / 2;
    if (i <= m) return query(i, lson);
    else return cnt[rt] - cnt[rs[rt]] + query(i, rson);
}
void dfs(int u, int f) {
    for (auto& x: edge[u]) {
        int v = x.first;
        if (v == f) continue;
        int w = lower_bound(lsh.begin(), lsh.end(), x.second) - lsh.begin() + 1;
        update(w, root[u], 1, nn, root[v]);
        dfs(v, u);
    }
}

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 2, u, v, w; i <= n; i++) {
        scanf("%d%d%d", &u, &v, &w);
        lsh.push_back(w);
        edge[u].push_back({v, w});
        edge[v].push_back({u, w});
    } hld::init();
    sort(lsh.begin(), lsh.end()); 
    lsh.resize(unique(lsh.begin(), lsh.end()) - lsh.begin());
    nn = lsh.size();
    dfs(1, 0);
    while (q--) {
        int u, v, k; scanf("%d%d%d", &u, &v, &k);
        int kk = lower_bound(lsh.begin(), lsh.end(), k) - lsh.begin();
        if (lsh[kk] == k) kk++;
        if (kk == 0) {
            puts("0"); continue;
        }
        int g = hld::qlca(u, v);
        int s1 = query(kk, 1, nn, root[g]);
        int s2 = query(kk, 1, nn, root[u]);
        int s3 = query(kk, 1, nn, root[v]);
        // dbg(u, v, g, s1, s2, s3);
        printf("%d\n", s2 + s3 - 2 * s1);
    }
    return 0;
}
```

## K

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

int n, q, a[maxn], b[maxn];
int pre[maxn];

int g(int l, int r) {
    if ((r - l + 1) % 2 == 0) return 0;
    return b[r] ^ b[l - 2];
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        a[0] = b[0] = pre[0] = 0;
        scanf("%d", &n);
        for (int i = 1; i <= n; i++) scanf("%d", a + i), a[i] ^= a[i - 1];
        for (int i = 1; i <= n; i++) b[i] = b[i - 1] ^ a[i];
        for (int i = 1; i <= n; i++) {
            pre[i] = pre[max(i - 2, 0)] ^ b[i];
        }
        scanf("%d", &q);
        while (q--) {
            int l, r; scanf("%d%d", &l, &r);
            int le = r - l;
            int f1 = (le / 2 + 1) & 1, f2 = (le / 2 + (le % 2)) & 1;
            int ans = 0;
            if (f1) {
                ans ^= pre[max(l - 4, 0)];
                int rr = r;
                if ((r - l) % 2) rr--;
                // dbg(1, l - 2, rr);
                ans ^= pre[rr];
            }
            if (f2) {
                ans ^= pre[max(l - 3, 0)];
                int rr = r;
                if ((r - l) % 2 == 0) rr--;
                // dbg(2, l - 1, rr);
                ans ^= pre[rr];
            }
            printf("%d\n", ans);
        }
    }
    return 0;
}
```



## M

```c++
#include<iostream>
#include<string>
#include <string>
#include <cstring>
#include <cstdio>
using namespace std;
int nextc[1000010][30];
char str1[1000010];
char s[1000010]; 

int main() {
    scanf("%s",s + 1);
    int len = strlen(s + 1);
    for (int i = len; i >= 1; i--) {
        for (int j = 0; j < 26; j++) {
            nextc[i - 1][j] = nextc[i][j]; 
        }
        nextc[i - 1][s[i] - 'a'] = i; 
    }
    int t;
    cin >> t;
    while(t--) {
        scanf("%s",str1);
        int len1 = strlen(str1);
        bool flag = false;
        for (int i = 0, now = 0; i < len1; i++) {
            now = nextc[now][str1[i] - 'a'];
            if (now == 0) {
                flag = true;
                break;
            }
        }
        if (flag) {
            puts("NO");
        } else {
            puts("YES");
        }
    }
    return 0;
}
```
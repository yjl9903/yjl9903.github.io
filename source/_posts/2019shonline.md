---
mathjax: true
title: 2019 年 ICPC 上海网络赛
tags:
  - Trainings
  - Tree
  - Data Structure
categories:
  - 训练
  - 网络赛
date: 2019-09-15 18:42:14
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  74  |   7    |  O  |  O  |  !  |  O  |  O  |  O  |  !  |  .  |  .  |  O  |  .  |  O  |

<!--more-->

# A

Solved by XLor.

一个结论：树上两个点集之间的直径，一定是从两个点集内部的某一对直径端点，共 $4$ 个点的某一个距离。

见 [动态直径](https://xlor.cn/2019/9/2019ceoid1t2/) 题解。

# B

Solved by XLor.

翻转操作没有时间顺序，离线所有事件，扫描线，统计左闭右开区间。

# C

UnSolved by Forsaken.

# D

Solved by Henry.

打表。

# E

Solved by Forsaken.

枚举偶数数字总个数$2k$,$ans=\sum_{k=0}^{\lfloor \frac{n}{2}\rfloor} C(n,2k)cnt_{1}^{n-2k}f(2k)$

$f(2k)$为用$cnt_{0}$种球放$2k$个盒子，且每种球个数为偶数的方案数

考虑$f(n)$为用$m$种球放$n$个盒子的方案：

则方案数为$\prod_{x_1+x_2+..x_m=n,x_i为偶数} \frac{n!}{x_1!x_2!...x_m!}$

构造指数型生成函数$g(x)=1+\frac{x^2}{2!}+\frac{x^4}{4!}+...=\frac{e^x+e^{-x}}{2}$

则$f(n)=g(x)^m[x^n]*n!$

$g(x)^m=\frac{\sum_{i=0}^{m}C(m,i)e^{(m-2i)x}}{2^m}$

所以$g(x)^m[x^n]*n!=\frac{\sum_{i=0}^{m}C(m,i)(m-2i)^n}{2^m}$

$ans=\sum_{k=0}^{\lfloor \frac{n}{2}\rfloor} C(n,2k)cnt_{1}^ninvcnt_1^{2k}\frac{\sum_{i=0}^{cnt_0}C(cnt_0,i)(cnt_0-2i)^{2k}}{2^{cnt_0}}$

$ans= \frac {cnt_{1}^{n}}{2^{cnt_0}}\sum_{i=0}^{cnt_0}C(cnt_0,i)\sum_{k=0}^{\lfloor \frac{n}{2}\rfloor}C(n,2k)(invcnt_1(cnt_0-2i))^{2k}$

$ans=\frac {cnt_{1}^{n}}{2^{cnt_0}}\sum_{i=0}^{cnt_0}C(cnt_0,i)\frac{(1+invcnt_1(cnt_0-2i))^n+(1-invcnt_1(cnt_0-2i))^n}{2}$

# F

Solved by Henry.

从左到右枚举，计算后缀方案数。

# G

UnSolved by XLor.

最多出现根号种长度，离线询问暴力哈希即可。

被卡常！QAQ。

# J

Solved by Henry.

# L

Solved by Henry.

# 代码

## A

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <string>
#include <utility>
#ifdef XLor
  #define dbg(args...) do { cout << #args << " -> "; err(args); } while (0)
  void err() { std::cout << std::endl; }
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
const int maxn = 100000 + 5;

struct Edge {
    int u, v, w;
} eg[maxn];

vector<PII> edge[maxn];
int n, q, tin[maxn], tout[maxn], rid[maxn], tot;

namespace hld {
    struct BIT {
        ll a[maxn];
        inline int lowbit(int x) {
            return x & -x;
        }
        inline void update(int i, int x) {
            for (; i <= n; i += lowbit(i)) a[i] += x;
        }
        inline void update(int l, int r, int x) {
            update(l, x); update(r + 1, -x);
        }
        inline ll query(int i) {
            ll r = 0;
            for (; i; i -= lowbit(i)) r += a[i];
            return r;
        }
    } g;

    int siz[maxn], dep[maxn], fa[maxn], son[maxn], top[maxn];
    void dfs(int u, int f) {
        tin[u] = ++tot; rid[tot] = u;
        dep[u] = dep[f] + 1; fa[u] = f; siz[u] = 1; son[u] = 0;
        int m = -1;
        for (auto& x: edge[u]) {
            int v = x.first;
            if (v == f) continue;
            dfs(v, u);
            g.update(tin[v], tout[v], x.second);
            siz[u] += siz[v];
            if (siz[v] > m) son[u] = v, m = siz[v];
        }
        tout[u] = tot;
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
    void build() {
        dfs(1, 0); dfs(1, 0, 1);
    }
    int qlca(int u, int v) {
        while (top[u] != top[v]){
            if (dep[top[u]] < dep[top[v]]) swap(u, v);
            u = fa[top[u]];
        }
        return dep[u] < dep[v] ? u : v;
    }
    ll qdis(int u, int v) {
        ll r = g.query(tin[u]) + g.query(tin[v]);
        int l = qlca(u, v);
        return r - 2ll * g.query(tin[l]);
    }
}
using hld::qdis;

struct Node {
    int u, v; ll d;
} a[maxn << 2];
void pushup(int rt) {
    if (a[rt << 1].d > a[rt << 1 | 1].d) {
        a[rt] = a[rt << 1];
    } else {
        a[rt] = a[rt << 1 | 1];
    }
    int x = a[rt << 1].u, y = a[rt << 1].v;
    int u = a[rt << 1 | 1].u, v = a[rt << 1 | 1].v;
    ll tot;
    if ((tot = qdis(x, u)) > a[rt].d) a[rt] = (Node){ x, u, tot };
    if ((tot = qdis(x, v)) > a[rt].d) a[rt] = (Node){ x, v, tot };
    if ((tot = qdis(y, u)) > a[rt].d) a[rt] = (Node){ y, u, tot };
    if ((tot = qdis(y, v)) > a[rt].d) a[rt] = (Node){ y, v, tot };
}
void build(int l = 1, int r = n, int rt = 1) {
    if (l == r) {
        int u = rid[l];
        a[rt] = (Node){ u, u, 0 };
        return ;
    }
    int m = (l + r) / 2;
    build(lson);  build(rson);
    pushup(rt);
}
void update(int L, int R, int l = 1, int r = n, int rt = 1) {
    if (L <= l && r <= R) return ;
    int m = (l + r) / 2;
    if (L <= m) update(L, R, lson);
    if (R > m) update(L, R, rson);
    pushup(rt);
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i < n; i++) {
        scanf("%d%d%d", &eg[i].u, &eg[i].v, &eg[i].w);
        edge[eg[i].u].push_back({ eg[i].v, eg[i].w });
        edge[eg[i].v].push_back({ eg[i].u, eg[i].w });
    }
    hld::build(); build();
    scanf("%d", &q);
    char op[5]; int e, v, w;
    for (int i = 1; i <= q; i++) {
        scanf("%s", op);
        if (op[0] == 'C') {
            scanf("%d%d", &e, &w);
            int u = eg[e].u, v = eg[e].v, ww = eg[e].w;
            if (hld::fa[v] != u) swap(u, v);
            hld::g.update(tin[v], tout[v], w - ww);
            eg[e].w = w;
            update(tin[v], tout[v]);
        } else if (op[0] == 'Q') {
            scanf("%d", &v);
            int x = a[1].u, y = a[1].v;
            // dbg(x, y, a[1].d);
            printf("%lld\n", max(qdis(x, v), qdis(y, v)));
        }
    }
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
#include <string>
#include <utility>
#ifdef XLor
  #define dbg(args...) do { cout << #args << " -> "; err(args); } while (0)
  void err() { std::cout << std::endl; }
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
const int maxn = 1000000 + 5;

int n, q;

int main() {
    int T, kase = 0; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &q);
        vector<PII> que;
        for (int i = 1, l, r; i <= q; i++) {
            scanf("%d%d", &l, &r); l++; r++;
            que.push_back({l, 1});
            que.push_back({r + 1, -1});
        }
        sort(que.begin(), que.end());
        int ans = 0, tot = 0, m = (int)que.size();
        for (int i = 0, j; i < m; i = j) {
            j = i;
            while (j < m && que[j].first == que[i].first) {
                tot += que[j].second; j++;
            }
            if (tot % 2 && j < m) {
                int nex = que[j].first;
                ans += nex - que[i].first;
            }
            // dbg(que[i].first);
        }
        printf("Case #%d: %d\n", ++kase, ans);
    }
    return 0;
}
```

## D

打表代码。

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const ll mod=1e9+7;
const int N=3001;
int t,n;
ll ans[N],C[N][N];

void dfs(int x, ll ji, ll he){
	for(int i=2; ji*i-he-i+x<N && i<N; i++){
		int tmp=ji*i-he-i;
		if(tmp>=0){
			ans[x+tmp]+=C[x+tmp][x];
			if(ans[x+tmp]>=mod) ans[x+tmp]-=mod;
		}
		dfs(x+1,ji*i,he+i);
	}
}

void init(){
	for(int i=0; i<N; i++){
		C[i][0]=C[i][i]=1;
		for(int j=1; j<i; j++){
			C[i][j]=C[i-1][j]+C[i-1][j-1];
			if(C[i][j]>=mod) C[i][j]-=mod;
		}
	}
	dfs(1,1,0);
}

int main(){
	init();
	for(int i=0; i<N; i++) printf("%lld,",ans[i]);
	return 0;
}
```

## E

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int mod=1e9+7;
const int max_n=200005;
inline int Pow(int x,ll y){
    y%=(mod-1);
    int res=1;
    while(y){
        if(y&1)res=1ll*res*x%mod;
        y>>=1;
        x=1ll*x*x%mod;
    }
    return res;
}
inline int Mul(int x,int y){
    return 1ll*x*y%mod;
}
inline int Add(int x,int y){
    return (x+=y)>=mod?x-mod:x;
}
inline int Sub(int x,int y){
    return (x-=y)<0?x+mod:x;
}
int T;
ll n;
int m;
int f[max_n],inv[max_n];
void init(int n){
    f[0]=1;
    for(int i=1;i<=n;i++)f[i]=Mul(f[i-1],i);
    inv[n]=Pow(f[n],mod-2);
    for(int i=n-1;i>=0;i--)inv[i]=Mul(inv[i+1],i+1);
}
int C(int n,int m){
    if(n<m||m<0)return 0;
    return 1ll*f[n]*inv[m]%mod*inv[n-m]%mod;
}
int inv2=Pow(2,mod-2);
int main(){
    init(max_n-1);
    scanf("%d",&T);
    while(T--){
        scanf("%lld%d",&n,&m);
        int c0=m/2,c1=m-c0;
        int x=Pow(c1,mod-2);
        int t1=Pow(inv2,c0);
        t1=Mul(t1,Pow(c1,n));
        int ans=0;
        for(int i=0;i<=c0;i++){
            int a=Add(1,Mul(x,Sub(c0,2*i)));
            int b=Sub(1,Mul(x,Sub(c0,2*i)));
            int tmp=Add(Pow(a,n),Pow(b,n));
            tmp=Mul(tmp,inv2);
            ans=Add(ans,Mul(C(c0,i),tmp));
        }
        ans=Mul(ans,t1);
        printf("%d\n",ans);
    }
    return 0;
}
```

## J

```c++
#include<bits/stdc++.h>
using namespace std;
const int N=302,mod=1e9+7;
int t,n,a[N],sum,tot,ans;
int dp[N*500];
int main(){
	scanf("%d",&t);
	while(t--){
		scanf("%d",&n);
		tot=0;
		for(int i=0; i<n; i++){
			scanf("%d",&a[i]);
			tot+=a[i];
		}
		sort(a,a+n);
		memset(dp,0,sizeof(dp));
		dp[0]=1;
		ans=0;
		sum=0;
		for(int i=n-1; i>=0; i--){
			sum+=a[i];
			for(int j=sum; j>=a[i]; j--){
				if(j>=tot-j && j-a[i]<=tot-j){
					ans+=dp[j-a[i]];
					if(ans>=mod) ans-=mod;
				}
				dp[j]+=dp[j-a[i]];
				if(dp[j]>=mod) dp[j]-=mod;
			}
		}
		printf("%d\n",ans);
	}
	return 0;
}
```

## L

```c++
#include<bits/stdc++.h>
using namespace std;
int t,n,b;

int getw(int x, int b){
	int ans=0;
	while(x){
		ans+=x%b;
		x/=b;
	}
	return ans;
}
int ans[1000005][11];

int main(){
	scanf("%d",&t);
	for(int i=1; i<=1000000; i++){
		for(int j=2; j<=10; j++){
			ans[i][j]=ans[i-1][j]+getw(i,j);
		}
	}
	for(int ti=1; ti<=t; ti++){
		scanf("%d%d",&n,&b);
		printf("Case #%d: %d\n",ti,ans[n][b]);
	}
	return 0;
}
```
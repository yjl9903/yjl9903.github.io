---
mathjax: true
title: 2019 年 ICPC 南京网络赛
tags:
  - Trainings
categories:
  - 训练
  - 网络赛
date: 2019-09-01 18:06:55
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  43  |   6    |  O  |  O  |  .  |  O  |  O  |  O  |  .  |  O  |  .  |

<!--more-->

# A

Solved by Henry.

$O(1)$ 计算出有效点值，离线询问矩形框，二维数点即可。

# B

Solved by Forsaken.

递归欧拉降幂模板题。

# C

UnSolved by Forsaken.

# D

Solved by XLor.

记 $f(u),g(u)$ 分别表示从 $u$ 开始到终点距离 $X$ 的期望 $E(X^2)$ 和 $E(X)$。

考虑从终点向起点转移，记 $v$ 是 $u$ 的儿子，$k$ 是 $u$ 的出度。

$$
g(u)=\sum {1 \over k+1} (1+g(v)) + {1 \over k+1}(1+g(u))
$$

移项得：

$$
{k \over k+1}g(u)=1+\sum g(v)
$$

因此：

$$
g(u)={k+1 \over k}+\sum g(v)
$$

考虑 $E(X^2)$ 的转移，$E((X+1)^2)=E(X^2)+2E(X)+1$。

类似地，可以写出：

$$
f(u)=\sum {1 \over k+1} (f(v)+2g(v)+1) + {1 \over k+1}(f(u)+2g(u)+1)
$$

因此：

$$
f(u)={k+1 \over k} + {2 \over k}g(u) + {1 \over k}\sum (f(v)+2g(v))
$$

答案就是 $(f(1)+g(1)) \over 2$。

# E

Solved by Forsaken.

推式子。

# F

Solved by XLor.

从小到大枚举每个值，询问区间最大值，单点更新。

# H

Solved by Henry.

做 $6$ 次 Floyd，每次连接最短路的相反数。

# 代码

## A

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
int t,n,m,p;

ll get(ll x, ll y){
	ll w=x-(n+1)/2,c=y-(n+1)/2;
	ll ji=max(abs(w),abs(c));
	ll ans;
	if(ji==0) ans=1;
	else if(w==ji){
		ans=(ji+ji+1)*(ji+ji+1)-ji+c;
	}
	else if(c==-ji){
		ans=(ji+ji+1)*(ji+ji+1)-ji*3+w;
	}
	else if(w==-ji){
		ans=(ji+ji+1)*(ji+ji+1)-ji*5-c;
	}
	else if(c==ji){
		ans=(ji+ji+1)*(ji+ji+1)-ji*7-w;
	}
	ans=1ll*n*n+1-ans;
	ll tmp=0;
	while(ans){
		tmp+=ans%10;
		ans/=10;
	}
	return tmp;
}

struct node{
	int x, y, v, tp;
}a[500001];
int cnt;
bool cmp(node a, node b){
	if(a.x!=b.x) return a.x<b.x;
	return abs(a.tp)<abs(b.tp);
}
int x,y,rx,ry;
ll ans[100001];
ll d[1000002];

void add(int x,int a){
	for(;x<=n;x+=x&-x){
		d[x]+=a;
	}
}

ll query(int x){
	ll ans=0;
	for(; x; x-=x&-x){
		ans+=d[x];
	}
	return ans;
}

int main(){
	scanf("%d",&t);
	while(t--){
		cnt=0;
		memset(d,0,sizeof(d));
		scanf("%d%d%d",&n,&m,&p);
		for(int i=0; i<m; i++){
			scanf("%d%d",&x,&y);
			a[cnt++]=(node){x,y,get(x,y),0};
		}
		for(int i=0; i<p; i++){
			ans[i]=0;
			scanf("%d%d%d%d",&x,&y,&rx,&ry);
			a[cnt++]=(node){rx ,ry ,i,1};
			a[cnt++]=(node){rx ,y-1,i,-1};
			a[cnt++]=(node){x-1,ry ,i,-1};
			a[cnt++]=(node){x-1,y-1,i,1};
		}
		sort(a,a+cnt,cmp);
		for(int i=0; i<cnt; i++){
			if(a[i].tp==0) add(a[i].y,a[i].v);
			else ans[a[i].v]+=a[i].tp*query(a[i].y);
		}
		for(int i=0; i<p; i++) printf("%lld\n",ans[i]);
	}
	return 0;
}
```

## B

```c++
#include<bits/stdc++.h>
#define Mod(a,b) a<b?a:a%b+b
//this Mod is important
using namespace std;
typedef long long ll;
const int max_n=1000005;
int a[max_n];
unordered_map<int,int> phi;
ll quick_pow(ll a,ll b,ll mod)
{
    ll res=1;
    while(b)
    {
        if(b&1)res=Mod(res*a,mod);//Mod
        b>>=1;
        a=Mod(a*a,mod);
    }
    return res;
}
int Phi(int n)
{
    if(phi[n])return phi[n];
    int res=n,x=n;//int x=n is a good haibit
    for(int i=2;i<=sqrt(x);i++)
    {
        if(x%i==0)
        {
            while(x%i==0)x/=i;
            res=res/i*(i-1);
        }
    }
    if(x!=1)res=res/x*(x-1);
    return phi[n]=res;
}
ll solve(int l,int r,ll mod)//Mod
{
    if(l==r||mod==1)return Mod(a[l],mod);
    return quick_pow(a[l],solve(l+1,r,Phi(mod)),mod);
}
int T;
int x,y,mod;
int main()
{
    scanf("%d",&T);
    while(T--){
        scanf("%d%d%d",&x,&y,&mod);
        for(int i=1;i<=y;i++)a[i]=x;
        if(y==0)printf("%d\n",1%mod);
        else printf("%lld\n",solve(1,y,mod)%mod);
    }
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
  #define dbg(args...) do { cout << #args << " -> "; err(args); } while (0)
  void err() { std::cout << std::endl; }
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

int n, m;
vector<int> edge[maxn];

double f[maxn], g[maxn];
int vis[maxn];
void dfs(int u) {
    if (vis[u]) return ;
    vis[u] = 1;
    double k = 1.0 * (int)edge[u].size();
    f[u] = g[u] = 1 + 1.0 / k;
    double sumf = 0, sumg = 0;
    for (int v: edge[u]) {
        dfs(v);
        sumf += f[v];
        sumg += g[v];
    }
    g[u] += sumg / k;
    f[u] += (sumf + 2.0 * sumg + 2.0 * g[u]) / k;
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        for (int i = 1; i <= n; i++) {
            vis[i] = 0;
            edge[i].clear();
        }
        for (int i = 1, u, v; i <= m; i++) {
            scanf("%d%d", &u, &v);
            edge[u].push_back(v);
        }
        vis[n] = 1; f[n] = g[n] = 0;
        dfs(1);
        printf("%.2lf\n", (f[1] + g[1]) / 2.0);
    }
    return 0;
}
```

## E

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int mod=1e9+7;
const int max_n=3000001;
inline int Add(int x,int y){
    return (x+=y)>=mod?x-mod:x;
}
inline int Mul(int x,int y){
    return 1ll*x*y%mod;
}
inline int Sub(int x,int y){
    return (x-=y)>=0?x:x+mod;
}
inline int Pow(int x,int y){
    if(x==0)return 0;
    int res=1;
    while(y){
        if(y&1)res=1ll*res*x%mod;
        y>>=1;
        x=1ll*x*x%mod;
    }
    return res;
}
bool p[max_n];
int pri[max_n],cnt;
int divisor[max_n];
int f[max_n];
int sum[max_n];
void sieve(int n){
    p[0]=p[1]=true;
    f[1]=1;
    for(int i=2;i<=n;i++){
        if(!p[i])pri[++cnt]=i,f[i]=Sub(Mul(i,i),1),divisor[i]=i;
        for(int j=1;j<=cnt&&1ll*i*pri[j]<=n;j++){
            p[i*pri[j]]=true;
            if(i%pri[j]){
                divisor[i*pri[j]]=pri[j];
                f[i*pri[j]]=Mul(f[i],f[pri[j]]);
            }
            else{
                divisor[i*pri[j]]=divisor[i]*pri[j];
                if(i==divisor[i])f[i*pri[j]]=Mul(f[i],Mul(pri[j],pri[j]));
                else f[i*pri[j]]=Mul(f[i/divisor[i]],f[divisor[i*pri[j]]]);
                break;
            }
        }
    }
    for(int i=1;i<=n;i++)sum[i]=Add(sum[i-1],f[i]);
}
int T;
char s[max_n];
int n;
int cal(char *s,int mod){
    int res=0;
    int len=strlen(s+1);
    for(int i=1;i<=len;i++)res=(1ll*res*10+s[i]-'0')%mod;
    return res;
}
int kk;
int qsum(int a){
    //return Add(a*a,a*a*a);
    if(a==1)return Sub(cal(s,mod),1);
    int inv=Pow(a-1,mod-2);
    int tk=kk+1;    
    if(tk==mod-1)tk=0;
    int res=Pow(a,tk);
    res=Sub(res,Mul(a,a));
    res=Mul(res,inv);
    return res;
}
int inv6;
int s2(int n){
    return 1ll*n*(n+1)%mod*(2*n+1)%mod*inv6%mod;
}
unordered_map<int,int> mp;
int S(int n){
    if(n<max_n)return sum[n];
    if(mp.count(n))return mp[n];
    int res=s2(n);
    for(int l=2,r;l<=n;l=r+1){
        r=n/(n/l);
        res=Sub(res,1ll*(r-l+1)*S(n/l)%mod);
    }
    return mp[n]=res;
}
int solve(int n){
    int res=0;
    for(int l=1,r;l<=n;l=r+1){
        r=n/(n/l);
        int x=qsum(n/l);
        res=Add(res,Mul(x,Sub(S(r),S(l-1))));
    }
    return res;
}
int baoli(int n){
    int res=0;
    /*
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            int d=__gcd(i,j);
            d*=d;
            res=Add(res,d);
        }
    }
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            for(int k=1;k<=n;k++){
                int d=__gcd(i,j);d=__gcd(d,k);
                d=d*d;
                res=Add(res,d);
            }
        }
    }
    */
   for(int i=1;i<=n;i++){
       res=Add(res,Mul(f[i],qsum(n/i)));
   }
    return res;
}
int main(){
    inv6=Pow(6,mod-2);
    sieve(max_n-1);
    scanf("%d",&T);
    while(T--){
        scanf("%d %s",&n,s+1);
        kk=cal(s,mod-1);
        //cout<<kk<<endl;
        printf("%d\n",solve(n));
        //cout<<baoli(n)<<endl;
    }
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

int n, k, a[maxn], id[maxn], ans[maxn];

int b[maxn * 4], tag[maxn * 4];
void build(int l, int r, int rt) {
    b[rt] = tag[rt] = 0;
    if (l == r) {
        return ;
    }
    int m = (l + r) / 2;
    build(lson); build(rson);
}
void pushdown(int rt) {
    if (tag[rt]) {
        b[rt << 1] = tag[rt];
        b[rt << 1 | 1] = tag[rt];
        tag[rt << 1] = tag[rt];
        tag[rt << 1 | 1] = tag[rt];
        tag[rt] = 0;
    }
}
void update(int L, int R, int x, int l = 1, int r = n, int rt = 1) {
    if (L <= l && r <= R) {
        b[rt] = tag[rt] = x;
        return ;
    }
    int m = (l + r) / 2; pushdown(rt);
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    b[rt] = max(b[rt << 1], b[rt << 1 | 1]);
}
int query(int L, int R, int l = 1, int r = n, int rt = 1) {
    if (L <= l && r <= R) return b[rt];
    int m = (l + r) / 2, ans = 0;
    pushdown(rt);
    if (L <= m) ans = query(L, R, lson);
    if (R > m) ans = max(ans, query(L, R, rson));
    return ans;
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &k);
        for (int i = 1; i <= n; i++) {
            scanf("%d", a + i);
            id[a[i]] = i;
        }
        build(1, n, 1);
        ans[1] = 1;
        update(id[1], id[1], 1);
        for (int i = 2; i <= n; i++) {
            int tl = max(1, id[i] - k), tr = min(n, id[i] + k);
            int tot = query(tl, tr);
            // dbg(tl, tr, tot);
            if (tot == 0) ans[i] = 1;
            else {
                ans[i] = 1 + ans[tot];
            }
            update(id[i], id[i], i);
        }
        for (int i = 1; i <= n; i++) printf("%d%c", ans[i], " \n"[i == n]);
    }
    return 0;
}
```

## H

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int N=301;
const ll inf=1e18;
int tt,n,m,x,y,w,s,t;
ll d[N][N],ans;
int main(){
	scanf("%d",&tt);
	while(tt--){
		scanf("%d%d",&n,&m);
		for(int i=0; i<n; i++){
			for(int j=0; j<n; j++){
				if(i==j) d[i][j]=0;
				else d[i][j]=inf;
			}
		}
		for(int i=0; i<m; i++){
			scanf("%d%d%d",&x,&y,&w);
			d[x][y]=w;
		}
		for(int z=0; z<6; z++){
			scanf("%d%d",&s,&t);
			for(int k=0; k<n; k++){
				for(int i=0; i<n; i++){
					for(int j=0; j<n; j++){
						if(d[i][k]!=inf && d[k][j]!=inf)
							d[i][j]=min(d[i][j],d[i][k]+d[k][j]);
					}
				}
			}
			printf("%lld\n",-d[t][s]);
			d[s][t]=-d[t][s];
		}
	}
	return 0;
}
```
---
mathjax: true
title: 2019 年 ICPC 沈阳网络赛
tags:
  - Trainings
categories:
  - 训练
  - 网络赛
date: 2019-09-14 20:54:58
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  31  |   10   |  O  |  O  |  O  |  O  |  O  |  O  |  O  |  O  |  .  |  O  |  O  |

<!--more-->

# A

Solved by Henry.

博弈。

# B

Solved by XLor.

与 $1$ 号点连接的安全点联通块全部都选，枚举该联通块内的点连向外部非安全点的边，期望是并查集的大小的和除以读数，取最大期望即可。

# C

Solved by Henry.

# D

Solved by XLor.

树形 dp。

# E

Solved by Forsaken.

百度到贝尔数的结论 $B_{p+n} \equiv B_{n}+B_{n+1} (\bmod p)$，记忆化一下即可。

# F

Solved by Henry.

# G

Solved by Henry.

# H

Solved by XLor.

模拟。

# J

Solved by Forsaken.

注意到大于一半的环最多有一个。

# K

Solved by XLor.

读错题了。

# 代码

## A

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const ll mod=1025436931;
int t;
int n,m,r1,c1,r2,c2;

struct mat{
	ll a[2][2];
};

mat mul(mat a, mat b){
	mat ans;
	memset(ans.a,0,sizeof(ans.a));
	for(int i=0; i<2; i++){
		for(int j=0; j<2; j++){
			for(int k=0; k<2; k++){
				ans.a[i][j]=(ans.a[i][j]+a.a[i][k]*b.a[k][j])%mod;
			}
		}
	}
	return ans;
}

mat qpow(mat x, ll y){
	mat ans;
	ans.a[0][0]=ans.a[1][1]=1;
	ans.a[0][1]=ans.a[1][0]=0;
	while(y){
		if(y&1) ans=mul(ans,x);
		y>>=1;
		x=mul(x,x);
	}
	return ans;
}

void f(ll x){
	assert(x>=0);
	mat ans;
	ans.a[0][0]=ans.a[0][1]=ans.a[1][0]=1;
	ans.a[1][1]=0;
	ans=qpow(ans,x);
	printf("%lld\n",(ans.a[0][0]+ans.a[0][1]+mod-1)%mod);
}


int main(){
	scanf("%d",&t);
	while(t--){
		scanf("%d%d%d%d%d%d",&n,&m,&r2,&c2,&r1,&c1);
		if((r1+c1)%2!=(r2+c2)%2){
			if(n==1){
				if(c1<c2) f(c2-1);
				else f(m-c2);
			}
			else if(m==1){
				if(r1<r2) f(r2-1);
				else f(n-r2);
			}
			else{
				printf("countless\n");
			}
		}
		else{
			if(r1>r2){
				r1=n+1-r1;
				r2=n+1-r2;
			}
			if(c1>c2){
				c1=m+1-c1;
				c2=m+1-c2;
			}
			if(r2-r1==c2-c1) f(r2+c2-3);
			else if(r2-r1>c2-c1) f(max(r2+c2-3,r2-c2+m-2));
			else f(max(r2+c2-3,c2-r2+n-2));
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
#include <set>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, m, k, unsafe[maxn], pre[maxn], siz[maxn], val[maxn];
vector<int> edge[maxn];

int find(int x) {
    return x == pre[x] ? x : pre[x] = find(pre[x]);
}
int join(int x, int y) {
    x = find(x); y = find(y);
    if (x == y) return false;
    pre[x] = y; siz[y] += siz[x];
    return true;
}

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d%d", &n, &m, &k);
        for (int i = 1; i <= n; i++) {
            edge[i].clear();
            pre[i] = i; 
            siz[i] = 1;
            unsafe[i] = val[i] = 0;
        }
        for (int i = 1, u, v; i <= m; i++) {
            scanf("%d%d", &u, &v);
            edge[u].push_back(v);
            edge[v].push_back(u);
        }
        for (int i = 1, x; i <= k; i++) {
            scanf("%d", &x); unsafe[x] = 1;
        }
        for (int i = 1; i <= n; i++) {
            if (unsafe[i]) continue;
            for (int v: edge[i]) {
                if (unsafe[v]) continue;
                join(i, v);
            }
        }
        for (int i = 1; i <= n; i++) {
            if (!unsafe[i]) continue;
            for (int v: edge[i]) {
                if (unsafe[v]) continue;
                if (find(v) == find(1)) continue;
                val[i] += siz[find(v)];
            }
        }
        double ans = siz[find(1)], mx = 0;
        for (int i = 1; i <= n; i++) {
            if (unsafe[i]) continue;
            if (find(i) != find(1)) continue;
            for (int v: edge[i]) {
                if (!unsafe[v]) continue;
                mx = max(mx, (double)val[v] / (double)edge[v].size());
            }
        }
        printf("%.8lf\n", ans + mx);
    }
    return 0;
}
```

## C

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
int n,m,p,c;
ll a,b;
ll dp[10005];
int main(){
	while(~scanf("%d%d",&n,&m)){
		memset(dp,-1ll,sizeof(dp));
		dp[0]=0;
		for(int i=1; i<=n; i++){
			scanf("%d%d",&p,&c);
			for(int j=0; j<m && j+c<=10000; j++){
				if(dp[j]!=-1){
					if(dp[j+c]==-1) dp[j+c]=dp[j]+p;
					else dp[j+c]=min(dp[j+c],dp[j]+p);
				}
			}
		}
		a=1e18;
		for(int i=m; i<=10000; i++){
			if(dp[i]!=-1 && dp[i]<=a){
				a=dp[i];
				b=i;
			}
		}
		printf("%lld %lld\n",a,b);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 1e9 + 7;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

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

int n;
vector<PII> edge[maxn];

int f[maxn][3], fc[maxn][3], g[maxn][3], gc[maxn][3], ans[3];
void dfs1(int u, int ff) {
    fc[u][0] = 1; fc[u][1] = fc[u][2] = 0;
    f[u][0] = f[u][1] = f[u][2] = 0;
    for (auto& x: edge[u]) {
        int v = x.first, w = x.second;
        if (v == ff) continue;
        dfs1(v, u);
        fc[u][w % 3] += fc[v][0];
        fc[u][(w + 1) % 3] += fc[v][1];
        fc[u][(w + 2) % 3] += fc[v][2];
        f[u][w % 3] = add(f[u][w % 3], add(f[v][0], mul(w, fc[v][0])));
        f[u][(w + 1) % 3] = add(f[u][(w + 1) % 3], add(f[v][1], mul(w, fc[v][1])));
        f[u][(w + 2) % 3] = add(f[u][(w + 2) % 3], add(f[v][2], mul(w, fc[v][2])));
    }
}

void dfs2(int u, int ff) {
    ans[0] = add(ans[0], g[u][0]);
    ans[1] = add(ans[1], g[u][1]);
    ans[2] = add(ans[2], g[u][2]);
    // dbg(u, ans[0], ans[1], ans[2]);

    for (auto& x: edge[u]) {
        int v = x.first, w = x.second;
        if (v == ff) continue;

        int tmp[3] = { g[u][0], g[u][1], g[u][2] };
        int tmpc[3] = { gc[u][0], gc[u][1], gc[u][2] };
        tmpc[w % 3] -= fc[v][0];
        tmpc[(w + 1) % 3] -= fc[v][1];
        tmpc[(w + 2) % 3] -= fc[v][2];
        tmp[w % 3] = sub(tmp[w % 3], add(f[v][0], mul(w, fc[v][0])));
        tmp[(w + 1) % 3] = sub(tmp[(w + 1) % 3], add(f[v][1], mul(w, fc[v][1])));
        tmp[(w + 2) % 3] = sub(tmp[(w + 2) % 3], add(f[v][2], mul(w, fc[v][2])));

        gc[v][w % 3] = fc[v][w % 3] + tmpc[0];
        gc[v][(w + 1) % 3] = fc[v][(w + 1) % 3] + tmpc[1];
        gc[v][(w + 2) % 3] = fc[v][(w + 2) % 3] + tmpc[2];
        g[v][0] = f[v][0];
        g[v][1] = f[v][1];
        g[v][2] = f[v][2];
        g[v][w % 3] = add(g[v][w % 3], add(tmp[0], mul(w, tmpc[0])));
        g[v][(w + 1) % 3] = add(g[v][(w + 1) % 3], add(tmp[1], mul(w, tmpc[1])));
        g[v][(w + 2) % 3] = add(g[v][(w + 2) % 3], add(tmp[2], mul(w, tmpc[2])));

        dfs2(v, u);
    }
}

int main() {
    while (scanf("%d", &n) == 1) {
        for (int i = 1; i <= n; i++) edge[i].clear();
        for (int i = 2, u, v, w; i <= n; i++) {
            scanf("%d%d%d", &u, &v, &w); u++; v++;
            edge[u].push_back({v, w});
            edge[v].push_back({u, w});
        }
        ans[0] = ans[1] = ans[2] = 0;
        dfs1(1, 0);
        g[1][0] = f[1][0]; gc[1][0] = fc[1][0];
        g[1][1] = f[1][1]; gc[1][1] = fc[1][1];
        g[1][2] = f[1][2]; gc[1][2] = fc[1][2];
        // cout << f[1][0] << ' ' << f[1][1] << ' ' << f[1][2];
        dfs2(1, 0);
        printf("%d %d %d\n", ans[0], ans[1], ans[2]);
    }
    return 0;
}
```

## E

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int max_n=1005;
int mod;
namespace {
    inline int Add(int x,int y){return (x+=y)>=mod?x-mod:x;}
    inline int Sub(int x,int y){return (x-=y)<0?x+mod:x;}
    inline int Mul(int x,int y) {return 1ll*x*y%mod;}
    inline int Pow(int x,int y){if(x==0)return 0;int res=1;while(y){if(y&1)res=1ll*res*x%mod;x=1ll*x*x%mod;y>>=1;}return res;}
}
ll f[max_n*2];
ll inv[max_n*2];
ll invn[max_n*2];
ll C(int n,int m)
{
    if(n<m||m<0)return 0;
    return f[n]*inv[n-m]%mod*inv[m]%mod;
}
int B[max_n];
void init(int n)
{
    f[0]=1;
    for(int i=1;i<=n;i++)f[i]=f[i-1]*i%mod;
    inv[n]=Pow(f[n],mod-2);
    for(int i=n-1;i>=0;i--)inv[i]=inv[i+1]*(i+1)%mod;
    invn[1]=1;
    for(int i=2;i<=n;i++)invn[i]=invn[mod%i]*(mod-mod/i)%mod;
    B[0]=1;
    for(int i=1;i<=n;i++){
        int res=0;
        for(int j=0;j<i;j++)res=Add(res,Mul(C(i-1,j),B[j]));
        B[i]=res;
    }
}
ll Lucas(ll n,ll m)
{
    if(n<m||m<0)return 0;
    if(m==0)return 1;
    return C(n%mod,m%mod)*Lucas(n/mod,m/mod)%mod;
}
int T;
int n;
int dp[1000001];
int cal(int n){
    if(dp[n])return dp[n];
    if(n<mod)return dp[n]=B[n];
    return dp[n]=Add(cal(n-mod),cal(n-mod+1));
}
int main(){
    scanf("%d",&T);
    while(T--){
        scanf("%d%d",&n,&mod);
        for(int i=0;i<=n;i++)dp[i]=0;
        init(mod-1);
        printf("%d\n",cal(n));
    }
    return 0;
}
```

## F

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
int n,k;
int a[500005],p,q;
ll sum,tmp;

int getpre(ll k){
	int num=0;
	for(int i=0; i<n; i++){
		num++;
		if(i==n-1 || 1ll*num*(a[i+1]-a[i])>k) return k/num+a[i];
		k-=1ll*(a[i+1]-a[i])*num;
	}
}
int getsuf(ll k){
	int num=0;
	for(int i=n-1; i>=0; i--){
		num++;
		if(i==0 || 1ll*num*(a[i]-a[i-1])>k) return a[i]-k/num;
		k-=1ll*(a[i]-a[i-1])*num;
	}
}

int main(){
	while(~scanf("%d%d",&n,&k)){
		sum=0;
		for(int i=0; i<n; i++){
			scanf("%d",&a[i]);
			sum+=a[i];
		}
		sort(a,a+n);
		tmp=0;
		for(int i=0; i<n-sum%n; i++) tmp+=abs(sum/n-a[i]);
		for(int i=n-sum%n; i<n; i++) tmp+=abs(sum/n+1-a[i]);
		tmp/=2;
		if(tmp<=k){
			printf("%d\n",sum%n?1:0);
			continue;
		}
		printf("%d\n",getsuf(k)-getpre(k));
	}
	return 0;
}
```

## G

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const double fi=(sqrt(5.0)+1.0)*0.5, eps=1e-7;
double a,ans;
char s[1000006];
int n,len;
int main(){
	int t;
	scanf("%d",&t);
	while(t--){
		scanf("%s%lf",s,&a);
		len=strlen(s);
		if(len>=8){
			printf("%.10lf\n",fi*a);
			continue;
		}
		ans=2;
		n=0;
		for(int i=0; i<len; i++) n=n*10+s[i]-'0';
		n=n*2-1;
		while(n--){
			ans=1.0+ans/(ans+1.0);
			if(fabs(ans-fi)*a<eps || n==0){
				printf("%.10lf\n",ans*a);
				break;
			}
		}
	}
	return 0;
}
```

## H

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <string>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

struct Node {
    string name;
    vector<int> card;
    int type, v1, v2, v3;
    Node() {}
    Node(string name, string cards) {
        this->name = name;
        for (int i = 0; i < (int)cards.length(); i++) {
            if (cards[i] == 'A') {
                card.push_back(1);
            } else if (cards[i] == '1') {
                i++;
                card.push_back(10);
            } else if (cards[i] == 'J') {
                card.push_back(11);
            } else if (cards[i] == 'Q') {
                card.push_back(12);
            } else if (cards[i] == 'K') {
                card.push_back(13);
            } else {
                card.push_back(cards[i] - '0');
            }
        }
        // cout << name << endl;
        // for (int x: card) cout << x << ' ';
        // cout << endl;
        vector<int> vis(15, 0);
        int sum = 0;
        for (int x: card) {
            vis[x]++; sum += x;
        }
        if (vis[10] && vis[11] && vis[12] && vis[13] && vis[1]) {
            type = 1; return ;
        }
        for (int i = 1; i + 4 <= 13; i++) {
            if (vis[i] && vis[i + 1] && vis[i + 2] && vis[i + 3] && vis[i + 4]) {
                type = 2; v1 = i + 4; return ;
            }
        }
        int two = 0, three = 0, four = 0;
        for (int i = 1; i <= 13; i++) {
            if (vis[i] == 2) {
                two++;
            } else if (vis[i] == 3) {
                three++;
            } else if (vis[i] == 4) {
                four++;
            }
        }
        if (four) {
            type = 3;
            for (int i = 1; i <= 13; i++) {
                if (vis[i] == 4) {
                    v1 = i;
                }
            }
            v2 = sum - 4 * v1;
        } else if (three && two) {
            type = 4;
            for (int i = 1; i <= 13; i++) {
                if (vis[i] == 3) {
                    v1 = i;
                } else if (vis[i] == 2) {
                    v2 = i;
                }
            }
        } else if (three) {
            type = 5;
            for (int i = 1; i <= 13; i++) {
                if (vis[i] == 3) {
                    v1 = i;
                    v2 = sum - v1 * 3;
                }
            }
        } else if (two == 2) {
            type = 6;
            v1 = v2 = -1;
            for (int i = 1; i <= 13; i++) {
                if (vis[i] == 2) {
                    if (v1 == -1) v1 = i;
                    else v2 = i;
                }
            }
            if (v1 < v2) swap(v1, v2);
            v3 = sum - 2 * v1 - 2 * v2;
        } else if (two == 1) {
            type = 7;
            for (int i = 1; i <= 13; i++) {
                if (vis[i] == 2) {
                    v1 = i;
                    v2 = sum - v1 * 2;
                }
            }
        } else {
            type = 8;
            v1 = sum;
        }
    }

    bool operator<(const Node& b) const {
        if (type != b.type) return type < b.type;
        if (type == 1) {

        } else if (type == 2) {
            if (v1 != b.v1) return v1 > b.v1;
        } else if (type == 3) {
            if (v1 != b.v1) return v1 > b.v1;
            if (v2 != b.v2) return v2 > b.v2;
        } else if (type == 4) {
            if (v1 != b.v1) return v1 > b.v1;
            if (v2 != b.v2) return v2 > b.v2;
        } else if (type == 5) {
            if (v1 != b.v1) return v1 > b.v1;
            if (v2 != b.v2) return v2 > b.v2;
        } else if (type == 6) {
            if (v1 != b.v1) return v1 > b.v1;
            if (v2 != b.v2) return v2 > b.v2;
            if (v3 != b.v3) return v3 > b.v3;            
        } else if (type == 7) {
            if (v1 != b.v1) return v1 > b.v1;
            if (v2 != b.v2) return v2 > b.v2;
        } else if (type == 8) {
            if (v1 != b.v1) return v1 > b.v1;
        }
        return name < b.name;
    }
};

int n;

int main() {
    while (scanf("%d", &n) == 1) {
        char s[20], t[20];
        vector<Node> v;
        for (int i = 1; i <= n; i++) {
            scanf("%s%s", s, t);
            v.push_back((Node){string(s), string(t)});
        }
        sort(v.begin(), v.end());
        for (auto& x: v) {
            printf("%s\n", x.name.c_str());
        }
    }
    return 0;
}
```

## J

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int mod=1e9+7;
const int max_n=1000005;
#define rint register int
namespace {
    inline int Add(int x,int y){return (x+=y)>=mod?x-mod:x;}
	inline int Sub(int x,int y){return (x-=y)<0?x+mod:x;}
    inline int Mul(int x,int y) {return 1ll*x*y%mod;}
    inline int Pow(int x,int y=mod-2){int res=1;while(y){if(y&1)res=1ll*res*x%mod;x=1ll*x*x%mod;y>>=1;}return res;}
}
int fac[max_n];
int inv[max_n];
void init(int n){
    fac[0]=1;
    for(int i=1;i<=n;i++)fac[i]=Mul(fac[i-1],i);
    inv[n]=Pow(fac[n]);
    for(int i=n-1;i>=0;i--)inv[i]=Mul(inv[i+1],i+1);
}
int C(int n,int m){
    return 1ll*fac[n]*inv[m]%mod*inv[n-m]%mod;
}
int T,n,x;
vector<int> v;
int main(){
    scanf("%d",&T);
    init(max_n-1);
    while(T--){
        scanf("%d%d",&n,&x);
        x=min(x,n);
        int all=fac[n];
        int ans=all;
        for(int i=x+1;i<=n;i++){
            int tmp=C(n,i);
            tmp=Mul(tmp,fac[i-1]);
            tmp=Mul(tmp,fac[n-i]);
            ans=Sub(ans,tmp);
        }
        ans=Mul(ans,Pow(all,mod-2));
        printf("%d\n",ans);
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
#include <string>
#include <utility>
#include <cassert>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 1e9 + 7;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

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

int k, a[maxn];
ll n;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%lld", &k, &n);
        for (int i = 1; i <= 2 * k; i++) {
            scanf("%d", a + i);
            if (i > k) {
                assert(a[i] == a[k + 1]);
            }
        }
        int ans = 0;
        if (n <= k) {
            for (int i = 1; i <= n; i++) ans = add(ans, a[i]);
        } else {
            for (int i = 1; i <= k; i++) ans = add(ans, a[i]);
            ans = add(ans, mul(a[k + 1], (n - k) % mod));
        }
        printf("%d\n", ans);
    }
    return 0;
}
```
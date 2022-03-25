---
mathjax: true
title: 2018 年宁夏邀请赛
tags:
  - Trainings
  - Data Structure
  - Tree
  - DP
  - Poly
  - FFT
  - Combinatorial
categories:
  - 训练
date: 2019-08-31 22:58:43
---

| solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  |
| :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|   10   |  O  |  O  |  O  |  O  |  O  |  O  |  Ø  |  O  |  .  |  .  |  .  |  Ø  |  O  |

<!--more-->

# A

Solved by Henry.

# B

Solved by Henry.

# C

Solved by XLor.

# D

Solved by XLor.

# E

Solved by Henry.

# F

Solved by XLor.

离线询问，按照权值排序，每次只能选权值的一个前缀的边，加入一条边对原图用 $Floyd$ 松弛一次即可。

# G

UpSolved by XLor.

记 $dp(i,j)$ 表示子树 $i$ 内选了 $j$ 个叶子时，对整个 $k$ 条边距离和贡献的最小值。

合并的时候考虑新的子树内的点个数，直接相加取 $\min$ 即可。

# H

Solved by XLor.

按照 $HP / ATK$ 降序的顺序击杀怪物。

# L

UpSolved by XLor.

对于区间 $l,r$，其最大值 $max$，最小值 $min$ 和不同数个数 $cnt$，满足 $max-min+1=cnt$ 为好的序列。

枚举右端点扫描线，由于 $max-min+1\ge cnt$，因此需要维护 $[l,r]$ 的 $max-min-cnt$ 的最小值和最小值个数，后缀的最值更新使用单调栈进行维护。

# M

Solved by Forsaken.

多项式。

# 代码

## A

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int N=5e6+1;
int t;
int n,p,q,m;
ll ans,tmp;
unsigned int SA,SB,SC;
unsigned int st[N],pp,mx[N];
void PUSH(unsigned int x){
	//cout<<"push"<<x<<endl;
	tmp++;
	st[++pp]=x;
	mx[pp]=max(mx[pp-1],st[pp]);
	ans^=mx[pp]*tmp;
}
void POP(){
	//cout<<"pop"<<endl;
	tmp++;
	if(pp==0) return;
	pp--;
	ans^=mx[pp]*tmp;
}
unsigned int rng61(){
	SA^=SA<<16;
	SA^=SA>>5;
	SA^=SA<<1;
	unsigned int t=SA; SA=SB;
	SB=SC;
	SC^=t^SA;
	return SC;
}
void gen(){
	ans=0;
	tmp=0;
	pp=0;
	scanf("%d%d%d%d%u%u%u",&n,&p,&q,&m,&SA,&SB,&SC);
	for(int i=1; i<=n; i++){
		if(rng61() % (p+q) <p)
				PUSH(rng61()%m+1);
		else
				POP();
	}
}



int main(){
	scanf("%d",&t);
	for(int i=1; i<=t; i++){
		gen();
		printf("Case #%d: %lld\n",i,ans);
	}
	return 0;
}
```

## B

```c++
#include<bits/stdc++.h>
using namespace std;
const int N=55;
const double pi=acos(-1);
int t,n;
struct node{
	double x,y;
}p[N],m;
double dis(node a, node b){
	return sqrt((a.x-b.x)*(a.x-b.x)+(a.y-b.y)*(a.y-b.y));
}
double arc(node a, node b){
	b.x-=a.x;
	b.y-=a.y;
	double fi;
	if(b.x==0){
		if(b.y>=0) fi=0.5*pi;
		else fi=pi+0.5*pi;
	}
	else if(b.x<0) fi=atan(b.y/b.x)+pi;
	else fi=atan(b.y/b.x);
	return fi;
}
double getarc(node a, node b, node c){
	double fi=arc(a,b)-arc(a,c);
	while(fi<=0) fi+=2*pi;
	while(fi>2*pi) fi-=2*pi;
	return fi;
}
int main(){
	scanf("%d",&t);
	for(int ti=1; ti<=t; ti++){
		scanf("%d",&n);
		for(int i=0; i<n; i++){
			scanf("%lf%lf",&p[i].x,&p[i].y);
		}
		scanf("%lf%lf",&m.x,&m.y);
		double ans=0;
		for(int i=0; i<n; i++){
			//cout<<i<<" "<<dis(m,p[i])<<" "<<getarc(m,p[(i-1+n)%n],p[(i+1)%n])<<endl;
			ans+=dis(m,p[i])*(pi-getarc(p[i],p[(i-1+n)%n],p[(i+1)%n]));
		}
		printf("Case #%d: %.3lf\n",ti,ans);
	}
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

char a[maxn], b[maxn], c[maxn];
int n, m;
char f[maxn];

int main() {
    int T, kase = 0; scanf("%d", &T);
    while (T--) {
        scanf("%d%d%s%s%s", &n, &m, a + 1, b + 1, c + 1);
        int offset = (a[1] - b[1] + 26) % 26;
        for (int i = 1; i <= m; i++) {
            c[i] = (c[i] - 'A' + offset) % 26 + 'A';
        }
        printf("Case #%d: ", ++kase);
        puts(c + 1);
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
#include <random>
#include <ctime>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 1000 + 5;

int n, m, p[maxn], vis[maxn];
mt19937 rnd(time(0));

int solve1(int n) {
    ms(vis, 0);
    auto get = [&]() {
        return rnd() % n + 1;
    };
    vis[get()] = 1;
    int flag = 0;
    for (int i = 2; i <= n; i++) {
        if (vis[p[i]]) {
            int x = get();
            while (vis[x]) x = get();
            vis[x] = 1;
            flag = 0;
        } else {
            vis[p[i]] = 1;
            flag = 1;
        }
    }
    return flag;
}

int main() {
    // cin >> n;
    // for (int i = 1; i <= n; i++) p[i] = i;
    // shuffle(p + 1, p + 1 + n, rnd);
    // int cnt = 0, T = 100000;
    // for (int i = 0; i < T; i++) {
    //     cnt += solve1(n);
    // }
    // cout << cnt << ' ' << T;
    int T, kase = 0; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        printf("Case #%d: ", ++kase);
        if (n == 1) printf("%.6lf ", 1.0);
        else printf("%.6lf ", 0.5);
        if (m == 1) printf("%.6lf\n", 1.0);
        else printf("%.6lf\n", 1.0 * (m + 1) / (2.0 * m));
    }
    return 0;
}
```

## E

```c++
#include<bits/stdc++.h>
using namespace std;
int t,n,x,rt,cnt;
struct node{
	int x[3],n;
	int s[4];
	int fa;
	bool leaf;
}a[5005];

void leafinsert(int u, int x, int s){
	a[u].n++;
	for(int i=a[u].n-1; i>=0; i--){
		if(i && a[u].x[i-1]>x){
			a[u].x[i]=a[u].x[i-1];
			if(!a[u].leaf) a[u].s[i+1]=a[u].s[i];
		}
		else{
			a[u].x[i]=x;
			if(!a[u].leaf) a[u].s[i+1]=s;
			return;
		}
	}
}

void output(int u){
	//cout<<u<<" "<<a[u].fa<<" : ";
	for(int i=0; i<a[u].n; i++){
		printf("%d%c",a[u].x[i],i==a[u].n-1?'\n':' ');
	}
	if(a[u].leaf==0){
		/*
		for(int i=0; i<=a[u].n; i++){
			printf("%d%c",a[u].s[i],i==a[u].n?'\n':' ');
		}
		*/
		for(int i=0; i<=a[u].n; i++){
			output(a[u].s[i]);
		}
	}
	return;
}

void insert(int u, int x){
	//cout<<"insert "<<u<<" "<<x<<endl;
	if(a[u].n==3){
		if(a[u].fa==-1){
			a[cnt].x[0]=a[u].x[1];
			a[cnt].n=1;
			a[cnt].s[0]=u;
			a[cnt].s[1]=cnt+1;
			a[cnt].fa=-1;
			a[cnt].leaf=0;
			a[u].fa=cnt;
			rt=cnt;
			cnt++;
		}
		else{
			leafinsert(a[u].fa,a[u].x[1],cnt);
		}
		a[cnt].x[0]=a[u].x[2];
		a[cnt].n=1;
		if(!a[u].leaf){
			a[cnt].s[0]=a[u].s[2];
			a[cnt].s[1]=a[u].s[3];
			a[a[u].s[2]].fa=cnt;
			a[a[u].s[3]].fa=cnt;
		}
		a[cnt].fa=a[u].fa;
		a[cnt].leaf=a[u].leaf;
		a[u].n=1;
		cnt++;
		if(x<a[u].x[1])
			insert(u,x);
		else
			insert(cnt-1,x);
	}
	else if(a[u].leaf==1){
		leafinsert(u,x,0);
	}
	else{
		for(int i=0; i<=a[u].n; i++){
			if(i-1>=0 && x<=a[u].x[i-1]) continue;
			if(i<a[u].n && x>=a[u].x[i]) continue;
			insert(a[u].s[i],x);
			break;
		}
	}
	return;
}


int main(){
	scanf("%d",&t);
	for(int ti=1; ti<=t; ti++){
		scanf("%d",&n);
		rt=0;
		cnt=1;
		a[0].n=0;
		a[0].fa=-1;
		a[0].leaf=1;
		for(int i=0; i<n; i++){
			scanf("%d",&x);
			insert(rt,x);
			//output(rt);
			//cout<<"hehe"<<endl;
		}
		printf("Case #%d:\n",ti);
		output(rt);
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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const ll inf = 1ll << 60;
const int maxn = 300 + 5;
const int maxm = 100000 + 5;

struct Que {
    int u, v, w, id;
    bool operator<(const Que& b) const {
        return w < b.w;
    }
};

int n, m, r[maxn], G[maxn][maxn];
ll dis[maxn][maxn], ans[maxm];

int main() {
    int T, kase = 0; scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        vector<PII> vec;
        for (int i = 1; i <= n; i++) scanf("%d", r + i), vec.push_back({r[i], i});
        sort(vec.begin(), vec.end());
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                scanf("%d", &G[i][j]);
                dis[i][j] = G[i][j];
            }
            dis[i][i] = 0;
        }
        vector<Que> que;
        for (int i = 1, u, v, w; i <= m; i++) {
            scanf("%d%d%d", &u, &v, &w);
            que.push_back({u, v, w, i});
        }
        sort(que.begin(), que.end());
        int j = 0;
        for (auto q: que) {
            while (j < n && vec[j].first <= q.w) {
                int k = vec[j].second; j++;
                for (int i = 1; i <= n; i++) {
                    for (int j = 1; j <= n; j++) {
                        if (dis[i][j] > dis[i][k] + dis[k][j]) {
                            dis[i][j] = dis[i][k] + dis[k][j];
                        }
                    }
                }
            }
            ans[q.id] = dis[q.u][q.v];
        }
        printf("Case #%d:\n", ++kase);
        for (int i = 1; i <= m; i++) printf("%lld\n", ans[i]);
    }
    return 0;
}
```

## G

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
const ll inf = 1ll << 40;
const int maxn = 100000 + 5;

struct fastIO {
    char s[100000]; int it,len;
    fastIO() { it = len = 0; }
    inline char get() {
        if (it < len) return s[it++];
        it = 0; len = fread(s, 1, 100000, stdin);
        if (len == 0) return EOF;
        else return s[it++];
    }
    bool notend() {
        char c = get();
        while(c == ' ' || c == '\n') c = get();
        if (it > 0) it--;
        return c != EOF;
    }
} buff;
inline int gi() {
    int r = 0; bool ng = 0; 
    char c = buff.get();
    while (c != '-' && (c < '0' || c > '9')) c = buff.get();
    if (c == '-') ng = 1, c = buff.get();
    while (c >= '0' && c <= '9') r = r * 10 + c - '0', c = buff.get();
    return ng ? -r : r;
}

int n, k, siz[maxn];
vector<PII> edge[maxn];

ll f[maxn][105], tmp[105];
void dfs(int u, int ff) {
    if ((int)edge[u].size() == 1) {
        f[u][1] = f[u][0] = 0;
        siz[u] = 1;
    }
    for (PII x: edge[u]) {
        int v = x.first, w = x.second;
        if (v == ff) continue;
        dfs(v, u);
        for (int i = 0; i <= k && i <= siz[u] + siz[v]; i++) tmp[i] = inf;
        for (int i = 0; i <= siz[u] && i <= k; i++) {
            for (int j = 0; i + j <= k && j <= siz[v]; j++) {
                ll tot = f[u][i] + f[v][j] + 1ll * j * (k - j) * w;
                tmp[i + j] = min(tmp[i + j], tot);
            }
        }
        siz[u] += siz[v];
        for (int i = 1; i <= k && i <= siz[u]; i++) f[u][i] = tmp[i];
    }
}

int main() {
    int T = gi(), kase = 0;
    while (T--) {
        n = gi(); k = gi();
        for (int i = 1; i <= n; i++) {
            edge[i].clear(); siz[i] = 0;
            for (int j = 1; j <= k; j++) {
                f[i][j] = inf;
            }
        }
        for (int i = 2, u, v, w; i <= n; i++) {
            u = gi(); v = gi(); w = gi();
            edge[u].push_back({v, w});
            edge[v].push_back({u, w});
        }
        printf("Case #%d: ", ++kase);
        if (k == 1) {
            puts("0"); continue;
        }
        if (n == 2) {
            printf("%d\n", edge[1][0].second); continue;
        }
        int rt = 1;
        for (int i = 1; i <= n; i++) {
            if ((int)edge[i].size() > 1) {
                rt = i; break;
            }
        }
        dfs(rt, 0);
        printf("%lld\n", f[rt][k]);
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
#include <utility>
#include <functional>
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

int n;
PII a[maxn];

int get(int x) {
    int l = 0, r = 1000, ans = -1;
    while (l <= r) {
        int m = (l + r) / 2;
        if (m * (m + 1) / 2 < x) ans = m + 1, l = m + 1;
        else r = m - 1;
    }
    dbg(x, ans);
    return ans;
}

int main() {
    int T, kase = 0; scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        ll ans = 0, suf = 0;
        for (int i = 1; i <= n; i++) {
            scanf("%d%d", &a[i].second, &a[i].first);
            a[i].second = get(a[i].second);
            suf += a[i].first;
        }
        sort(a + 1, a + 1 + n, [&](PII a, PII b) {
            return 1ll * a.first * b.second > 1ll * a.second * b.first;
        });
        for (int i = 1; i <= n; i++) {
            ans += suf * a[i].second;
            suf -= a[i].first;
        }
        printf("Case #%d: %lld\n", ++kase, ans);
    }
    return 0;
}
```

## L

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
  #define dbg(args...) do { cout << #args << " -> "; err(args); } while (0)
  void err() { std::cout << std::endl; }
  template<typename T, typename...Args>
  void err(T a, Args...args) { std::cout << a << ' '; err(args...); }
#else
  #define dbg(...)
#endif
#define ms(a,b) memset(a,b,sizeof(a))
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 100000 + 5;

int n, a[maxn];

struct Node {
    int val, cnt;
    void clear() {
        val = inf; cnt = 0;
    }
    Node operator+(const Node& b) const {
        if (val == b.val) return (Node){ val, cnt + b.cnt };
        else if (val < b.val) return (Node){ val, cnt };
        else return (Node){ b.val, b.cnt };
    }
    Node operator+=(const int& b) {
        val += b;
    }
} b[maxn * 4];
int tag[maxn * 4];
void build(int l, int r, int rt) {
    b[rt].clear(); tag[rt] = 0;
    if (l == r) {
        b[rt].cnt = 1; return ;     
    }
    int m = (l + r) / 2;
    build(lson); build(rson);
}
void pushdown(int rt) {
    if (!tag[rt]) return ;
    int& t = tag[rt];
    b[rt << 1] += t;
    tag[rt << 1] += t;
    b[rt << 1 | 1] += t;
    tag[rt << 1 | 1] += t;
    t = 0;
}
void update(int L, int R, int x, int l = 1, int r = n, int rt = 1) {
    if (L <= l && r <= R) {
        b[rt] += x; tag[rt] += x;
        return ;
    }
    int m = (l + r) / 2; pushdown(rt);
    if (L <= m) update(L, R, x, lson);
    if (R > m) update(L, R, x, rson);
    b[rt] = b[rt << 1] + b[rt << 1 | 1];
}

void print(int l = 1, int r = n, int rt = 1) {
    if (l == r) {
        dbg(l, b[rt].val);
        return ;
    }
    int m = (l + r) / 2; pushdown(rt);
    print(lson); print(rson);
}

int main() {
    int T, kase = 0; scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        build(1, n, 1);
        for (int i = 1; i <= n; i++) {
            scanf("%d", a + i);
        }
        ll ans = 0;
        map<int,int> last;
        vector<int> stmn, stmx;
        for (int i = 1; i <= n; i++) {
            update(i, i, -inf);
            int q = i, tot = 0;
            while (!stmn.empty() && a[stmn.back()] >= a[i]) {
                int p = stmn.back(); stmn.pop_back();
                update(p + 1, q, tot - a[i]);
                q = p; tot = a[p];
            }
            if (!stmn.empty()) {
                int p = stmn.back();
                update(p + 1, q, tot - a[i]);
            } else {
                update(1, q, tot - a[i]);
            }
            q = i; tot = 0;
            while (!stmx.empty() && a[stmx.back()] <= a[i]) {
                int p = stmx.back(); stmx.pop_back();
                update(p + 1, q, a[i] - tot);
                q = p; tot = a[p];
            }
            if (!stmx.empty()) {
                int p = stmx.back();
                update(p + 1, q, a[i] - tot);
            } else {
                update(1, q, a[i] - tot);
            }

            stmn.push_back(i);
            stmx.push_back(i);
            update(last[a[i]] + 1, i, -1);
            last[a[i]] = i;

            if (b[1].val == -1) ans += b[1].cnt;
        }
        printf("Case #%d: %lld\n", ++kase, ans);
    }    
    return 0;
}
```

## M

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int max_n=60005;
const double PI=acos(-1.0);
int mod;
namespace {
    inline int Add(int x,int y){return (x+=y)>=mod?x-mod:x;}
	inline int Sub(int x,int y){return (x-=y)<0?x+mod:x;}
    inline int Mul(int x,int y) {return 1ll*x*y%mod;}
    inline int Pow(int x,int y){int res=1;while(y){if(y&1)res=1ll*res*x%mod;x=1ll*x*x%mod;y>>=1;}return res;}
}

int Tonelli_Shanks(int n,ll p)
{
    if(p==2) return (n&1)?1:-1;
    if(Pow(n,p>>1)!=1) return -1;
    if(p&2) return Pow(n,(p+1)>>2);
    int s=__builtin_ctzll(p^1);
    int q=p>>s,z=2;
    for(;Pow(z,p>>1)==1;++z);
    int c=Pow(z,q),r=Pow(n,(q+1)>>1),t=Pow(n,q),tmp;
    for(int m=s,i;t!=1;)
    {
        for(i=0,tmp=t;tmp!=1;++i) tmp=tmp*tmp%p;
        for(;i<--m;) c=c*c%p;
        r=r*c%p;c=c*c%p;t=t*c%p;
    }
    return r;
}


namespace fft
{
    struct num
    {
        double x,y;
        num() {x=y=0;}
        num(double x,double y):x(x),y(y){}
    };
    inline num operator+(num a,num b) {return num(a.x+b.x,a.y+b.y);}
    inline num operator-(num a,num b) {return num(a.x-b.x,a.y-b.y);}
    inline num operator*(num a,num b) {return num(a.x*b.x-a.y*b.y,a.x*b.y+a.y*b.x);}
    inline num conj(num a) {return num(a.x,-a.y);}

    int base=1;
    vector<num> roots={{0,0},{1,0}};
    vector<int> rev={0,1};
    const double PI=acosl(-1.0);

    void ensure_base(int nbase)
    {
        if(nbase<=base) return;
        rev.resize(1<<nbase);
        for(int i=0;i<(1<<nbase);i++)
            rev[i]=(rev[i>>1]>>1)+((i&1)<<(nbase-1));
        roots.resize(1<<nbase);
        while(base<nbase)
        {
            double angle=2*PI/(1<<(base+1));
            for(int i=1<<(base-1);i<(1<<base);i++)
            {
                roots[i<<1]=roots[i];
                double angle_i=angle*(2*i+1-(1<<base));
                roots[(i<<1)+1]=num(cos(angle_i),sin(angle_i));
            }
            base++;
        }
    }

    void fft(vector<num> &a,int n=-1)
    {
        if(n==-1) n=a.size();
        assert((n&(n-1))==0);
        int zeros=__builtin_ctz(n);
        ensure_base(zeros);
        int shift=base-zeros;
        for(int i=0;i<n;i++)
            if(i<(rev[i]>>shift))
                swap(a[i],a[rev[i]>>shift]);
        for(int k=1;k<n;k<<=1)
        {
            for(int i=0;i<n;i+=2*k)
            {
                for(int j=0;j<k;j++)
                {
                    num z=a[i+j+k]*roots[j+k];
                    a[i+j+k]=a[i+j]-z;
                    a[i+j]=a[i+j]+z;
                }
            }
        }
    }

    vector<num> fa,fb;

    vector<int> multiply(vector<int> &a, vector<int> &b)
    {
        int need=a.size()+b.size()-1;
        int nbase=0;
        while((1<<nbase)<need) nbase++;
        ensure_base(nbase);
        int sz=1<<nbase;
        if(sz>(int)fa.size()) fa.resize(sz);
        for(int i=0;i<sz;i++)
        {
            int x=(i<(int)a.size()?a[i]:0);
            int y=(i<(int)b.size()?b[i]:0);
            fa[i]=num(x,y);
        }
        fft(fa,sz);
        num r(0,-0.25/sz);
        for(int i=0;i<=(sz>>1);i++)
        {
            int j=(sz-i)&(sz-1);
            num z=(fa[j]*fa[j]-conj(fa[i]*fa[i]))*r;
            if(i!=j) fa[j]=(fa[i]*fa[i]-conj(fa[j]*fa[j]))*r;
            fa[i]=z;
        }
        fft(fa,sz);
        vector<int> res(need);
        for(int i=0;i<need;i++) res[i]=fa[i].x+0.5;
        return res;
    }

    vector<int> multiply_mod(vector<int> &a,vector<int> &b,int m,int eq=0)
    {
        int need=a.size()+b.size()-1;
        int nbase=0;
        while((1<<nbase)<need) nbase++;
        ensure_base(nbase);
        int sz=1<<nbase;
        if(sz>(int)fa.size()) fa.resize(sz);
        for(int i=0;i<(int)a.size();i++)
        {
            int x=(a[i]%m+m)%m;
            fa[i]=num(x&((1<<15)-1),x>>15);
        }
        fill(fa.begin()+a.size(),fa.begin()+sz,num{0,0});
        fft(fa,sz);
        if(sz>(int)fb.size()) fb.resize(sz);
        if(eq) copy(fa.begin(),fa.begin()+sz,fb.begin());
        else
        {
            for(int i=0;i<(int)b.size();i++)
            {
                int x=(b[i]%m+m)%m;
                fb[i]=num(x&((1<<15)-1),x>>15);
            }
            fill(fb.begin()+b.size(),fb.begin()+sz,num{0,0});
            fft(fb,sz);
        }
        double ratio=0.25/sz;
        num r2(0,-1),r3(ratio,0),r4(0,-ratio),r5(0,1);
        for(int i=0;i<=(sz>>1);i++)
        {
            int j=(sz-i)&(sz-1);
            num a1=(fa[i]+conj(fa[j]));
            num a2=(fa[i]-conj(fa[j]))*r2;
            num b1=(fb[i]+conj(fb[j]))*r3;
            num b2=(fb[i]-conj(fb[j]))*r4;
            if(i!=j)
            {
                num c1=(fa[j]+conj(fa[i]));
                num c2=(fa[j]-conj(fa[i]))*r2;
                num d1=(fb[j]+conj(fb[i]))*r3;
                num d2=(fb[j]-conj(fb[i]))*r4;
                fa[i]=c1*d1+c2*d2*r5;
                fb[i]=c1*d2+c2*d1;
            }
            fa[j]=a1*b1+a2*b2*r5;
            fb[j]=a1*b2+a2*b1;
        }
        fft(fa,sz);fft(fb,sz);
        vector<int> res(need);
        for(int i=0;i<need;i++)
        {
            ll aa=fa[i].x+0.5;
            ll bb=fb[i].x+0.5;
            ll cc=fa[i].y+0.5;
            res[i]=(aa+((bb%m)<<15)+((cc%m)<<30))%m;
        }
        return res;
    }
    vector<int> square_mod(vector<int> &a,int m)
    {
        return multiply_mod(a,a,m,1);
    }
};


namespace poly
{
    int inv(int x) {return Pow(x,mod-2);}
    vector<int> fa,fb,fc,fd;
    vector<int> get_inv(vector<int> &a,int n)
    {
        assert(a[0]!=0);
        if(n==1)
        {
            fa.resize(1);
            fa[0]=inv(a[0]);
            return fa;
        }
        fa=get_inv(a,(n+1)>>1);
        fb=fft::multiply_mod(fa,fa,mod,1);
        fb=fft::multiply_mod(fb,a,mod);
        fa.resize(n);
        for(int i=0;i<n;i++)
        {
            fa[i]=Add(fa[i],fa[i]);
            fa[i]=Sub(fa[i],fb[i]);
        }
        return fa;
    }
    vector<int> get_sqrt(vector<int> &a,int n)
    {
        if(n==1)
        {
            fc.resize(1);
            int x=Tonelli_Shanks(a[0],mod);
            assert(x!=-1);
            fc[0]=x;return fc;
        }
        fd=get_sqrt(a,(n+1)>>1);
        fc=get_inv(fd,(n+1)>>1);
        fd=fft::multiply_mod(fd,fd,mod,1);
        for(int i=0;i<(n+1)/2;i++) fc[i]=1LL*fc[i]*((mod+1)/2)%mod;
        for(int i=0;i<n;i++) fd[i]=Add(fd[i],a[i]);
        fd=fft::multiply_mod(fd,fc,mod);
        fd.resize(n);return fd;
    }
    vector<int> diff(vector<int> &a)
    {
        for(int i=1;i<(int)a.size();i++) a[i-1]=1LL*a[i]*i%mod;
        if(a.size()>=1) a.resize((int)a.size()-1);
        return a;
    }
    vector<int> intg(vector<int> &a)
    {
        int sz=(int)a.size();
        a.resize(sz+1);
        static vector<int> Inv(sz+1);
        Inv[1]=1;
        for(int i=2;i<=sz;i++) Inv[i]=Sub(mod,1LL*Inv[mod%i]*(mod/i)%mod);
        for(int i=sz;i>=1;i--) a[i]=1LL*a[i-1]*Inv[i]%mod;
        a[0]=0;
        return a;
    }
};
int fac[max_n*4];
int inv[max_n*4];
void init(int n){
    fac[0]=1;
    for(int i=1;i<=n;i++)fac[i]=1ll*fac[i-1]*i%mod;
    inv[n]=Pow(fac[n],mod-2);
    for(int i=n-1;i>=0;i--)inv[i]=1ll*inv[i+1]*(i+1)%mod;
}
vector<int> cal(int n){
    vector<int> v1,v2;
    for(int i=0;i<=n;i++)v1.push_back(Mul(Pow(i,n),inv[i]));
    for(int i=0;i<=n;i++){
        if(i&1)v2.push_back(mod-inv[i]);
        else v2.push_back(inv[i]);
    }
    v1=fft::multiply_mod(v1,v2,mod);
    v1.resize(n+1);
    return v1;
}
int T;
int n,m;
int main(){
    scanf("%d",&T);
    int cas=1;
    while(T--){
        scanf("%d%d%d",&n,&m,&mod);
        int len=1;
        while(len<n+m+1)len<<=1;
        init(len);
        vector<int> v1=cal(n),v2=cal(m);
        v1=fft::multiply_mod(v1,v2,mod);
        int ans=0;
        for(int i=0;i<=n+m;i++){
            if(i&1)ans=Sub(ans,Mul(v1[i],fac[i]));
            else ans=Add(ans,Mul(v1[i],fac[i]));
        }
        if((n+m)&1)ans=mod-ans;
        printf("Case #%d: %d\n",cas++,ans);
    }
    return 0;
}
```
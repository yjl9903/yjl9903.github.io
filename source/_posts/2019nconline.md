---
mathjax: true
title: 2019 年 ICPC 南昌网络赛
tags:
  - Trainings
  - Suffix Automata
categories:
  - 训练
  - 网络赛
date: 2019-09-12 15:14:11
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  88  |   6    |  !  |  O  |  O  |  .  |  O  |  O  |  O  |  O  |  .  |

<!--more-->

# A

UnSolved by Forsaken.

# B

Solved by XLor.

# C

Solved by XLor.

线段树维护 $dp$ 转移矩阵的乘法。

# E

Solved by Henry.

链表模拟。

# F

Solved by XLor.

枚举每个本质不同子串，其长度为 $p$，对答案的总贡献为 $(n-p+1)26^{-p}$。

预处理出长度贡献的前缀和，SAM 上加字符时，直接计算贡献即可。

# G

Solved by Forsaken.

二次剩余推出式子后，光速幂。

# H

Solved by Forsaken.

# 代码

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
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const ll inf = 1e10;
const int maxn = 1000 + 5;

int n, m, s, k, c;
ll G[maxn][maxn];

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        vector<int> key;
        scanf("%d%d%d%d%d", &n, &m, &s, &k, &c);
        for (int i = 1, x; i <= k; i++) scanf("%d", &x), key.push_back(x);
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) G[i][j] = inf;
            G[i][i] = 0;
        }
        for (int i = 1, u, v, w; i <= m; i++) {
            scanf("%d%d%d", &u, &v, &w);
            G[u][v] = min(G[u][v], 1ll * w);
            G[v][u] = G[u][v];
        }
        for (int k = 1; k <= n; k++) {
            for (int i = 1; i <= n; i++) {
                for (int j = 1; j <= n; j++) {
                    G[i][j] = min(G[i][j], G[i][k] + G[k][j]);
                }
            }
        }
        ll hero = 0, mx = 0;
        for (int i = 1; i <= n; i++) hero = max(hero, G[s][i]);
        for (int i = 1; i <= n; i++) {
            ll mn = inf;
            for (int x: key) {
                mn = min(mn, G[x][i]);
            }
            mx = max(mx, mn);
        }
        if (hero <= 1ll * c * mx) printf("%lld\n", hero);
        else printf("%lld\n", mx);
    }
    return 0;
}
```

## C

```
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <string>
#include <utility>
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 25;
const int maxn = 200000 + 5;

struct Mat {
    static const int M = 5;
    int a[M][M];
    void clear() { 
        for (int i = 0; i < M; i++) 
            for (int j = 0; j < M; j++)
                a[i][j] = inf;
    }
    Mat() { clear(); }
    void eye() { for (int i = 0; i < M; i++) a[i][i] = 1; }
    int* operator [] (int x) { return a[x]; }
    const int* operator [] (int x) const { return a[x]; }
    Mat operator * (const Mat& b) {
        const Mat& a = *this; Mat r;
        for (int i = 0; i < M; i++)
            for (int j = 0; j < M; j++)
                for (int k = 0; k < M; k++)
                    r[i][j] = min(r[i][j], a[i][k] + b[k][j]);
        return r;
    }
} tr[maxn << 2];

int n, q;
char s[maxn];

void build(int l, int r, int rt) {
    tr[rt].clear();
    if (l == r) {
        for(int i = 0; i < 5; i++) tr[rt][i][i] = 0;
        if(s[l] == '2') tr[rt][0][0] = 1, tr[rt][0][1] = 0;
        if(s[l] == '0') tr[rt][1][1] = 1, tr[rt][1][2] = 0;
        if(s[l] == '1') tr[rt][2][2] = 1, tr[rt][2][3] = 0;
        if(s[l] == '9') tr[rt][3][3] = 1, tr[rt][3][4] = 0;
        if(s[l] == '8') tr[rt][3][3] = 1, tr[rt][4][4] = 1;
        return ;
    }
    int m = (l + r) / 2;
    build(rson); build(lson);
    tr[rt] = tr[rt << 1 | 1] * tr[rt << 1];
}
Mat query(int L, int R, int l, int r, int rt) {
    if (L <= l && r <= R) return tr[rt];
    int m = (l + r) / 2;
    if(R <= m) return query(L, R, lson);
    if(m < L) return query(L, R, rson);
    return query(L, R, rson) * query(L, R, lson);
}

int main() {
    scanf("%d%d%s", &n, &q, s + 1);
    build(1, n, 1);
    while (q--) {
        int l, r;
        scanf("%d%d", &l, &r);
        Mat ans = query(l, r, 1, n, 1);
        if (ans[0][4] > n) puts("-1");
        else printf("%d\n", ans[0][4]);
    }
    return 0;
}
```

## E

```c++
#include<bits/stdc++.h>
using namespace std;
int t,n,m,k,q;
const int N=4e7+5;
int nxt[N];
int ans[N];

int main(){
	scanf("%d",&t);
	while(t--){
		scanf("%d%d",&n,&m);
		m++;
		for(int i=1; i<=n; i++){
			if(i==n) nxt[i]=1;
			else nxt[i]=i+1;
		}
		int p=1,pre=n;
		for(int i=1; i<=n; i++){
			//cout<<p<<endl;
			ans[p]=i;
			nxt[pre]=nxt[p];
			if(i<n)
				for(int j=1; j<=m; j++){
					pre=p;
					p=nxt[p];
				}
		}
		scanf("%d",&q);
		while(q--){
			scanf("%d",&k);
			printf("%d\n",ans[k]);
		}
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
#include <string>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int,int> PII;
const int mod = 998244353;
const int inf = 1 << 30;
const int maxn = 400000 + 5;

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

int l, k, n, m, a[100], pre[maxn];
char s[maxn];

namespace sam {
    int tot, last, cnt[maxn << 1];
    int len[maxn << 1], link[maxn << 1], ch[maxn << 1][26];
    void clear() { 
        tot = last = 1; ms(ch[1], 0);
    }
    int insert(int c) {
        int cur = ++tot, p = last; 
        ms(ch[cur], 0);
        len[cur] = len[last] + 1; 
        cnt[cur] = 1;
        for (; p && !ch[p][c]; p = link[p]) ch[p][c] = cur;
        if (!p) link[cur] = 1;
        else {
            int q = ch[p][c];
            if (len[p] + 1 == len[q]) link[cur] = q;
            else {
                int nq = ++tot; 
                len[nq] = len[p] + 1; 
                cnt[nq] = 0;
                link[nq] = link[q]; 
                link[q] = link[cur] = nq;
                memcpy(ch[nq], ch[q], sizeof ch[q]);
                for (; ch[p][c] == q; p = link[p]) ch[p][c] = nq;
            }
        }
        return last = cur;
    }
}
using namespace sam;

int main() {
    int T; scanf("%d", &T);
    while (T--) {
        scanf("%d%d%d%d%s", &l, &k, &n, &m, s + 1);
        for (int i = 0; i <= k; i++) scanf("%d", a + i);
        for (int i = 1; i <= l + m; i++) {
            int x = 1, sum = 0;
            for (int j = 0; j <= k; j++) {
                sum = add(sum, mul(a[j], x));
                x = mul(x, i);
            }
            sum = mul(sum, n - i + 1);
            sum = mul(sum, inv(qpow(26, i)));
            pre[i] = add(pre[i - 1], sum);
        }
        sam::clear();
        int ans = 0;
        auto insert = [&](int c) -> void {
            int u = sam::insert(c);
            int l = len[link[u]], r = len[u];
            ans = add(ans, sub(pre[r], pre[l]));
        };
        for (int i = 1; i <= l; i++) insert(s[i] - 'a');
        printf("%d\n", ans);
        char op[5];
        while (m--) {
            scanf("%s", op);
            insert(op[0] - 'a');
            printf("%d\n", ans);
        }
    }
    return 0;
}
```

## G

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
int T;
int n;
int main(){
    scanf("%d",&T);
    while(T--){
        scanf("%d",&n);
        if(n==1)printf("18000\n");
        else printf("0\n");
    }
    return 0;
}
```

## H

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long 
#define re register
#define gc getchar
#define pc putchar
#define puts put_s
#define cs const
const int mod=998244353;
const int B=sqrt(mod)+1;

inline ll Pow(ll x,ll y=mod-2){
    if(x==0)return 0;
    y%=(mod-1);
    ll res=1;
    while(y){
        if(y&1)res=res*x%mod;
        y>>=1;
        x=1ll*x*x%mod;
    }
    return res;
}
ll inv=Pow(2);
int T;
ll x=473844410;
ll invx=Pow(x);
ll a,b;
ll f1[B*2],f2[B*2];
ll f3[B*2],f4[B*2];
ll Pow1(ll n){
    n%=(mod-1);
    return f1[n/B]*f2[n%B]%mod;
}
ll Pow2(ll n){
    n%=(mod-1);
    return f3[n/B]*f4[n%B]%mod;
}
ll cal(ll n){
    ll res=Pow1(n);
    res-=Pow2(n);
    res=(res+mod)%mod;
    res=res*invx%mod;
    return res;
}
int main(){
    a=(1ll*3+x)*inv%mod;
    b=(1ll*3-x+mod)*inv%mod;
    ll q=Pow(a,B);
    f1[0]=1;
    for(int i=1;i<=B;i++)f1[i]=q*f1[i-1]%mod;
    f2[0]=1;
    for(int i=1;i<=B;i++)f2[i]=a*f2[i-1]%mod;
    q=Pow(b,B);
    f3[0]=1;
    for(int i=1;i<=B;i++)f3[i]=f3[i-1]*q%mod;
    f4[0]=1;
    for(int i=1;i<=B;i++)f4[i]=f4[i-1]*b%mod;
	int Q;ll n;
    scanf("%d%lld",&Q,&n);
    ll pre=0;
    ll ans=0;
    while(Q--){
        n^=pre*pre;
        ll tmp=cal(n);
        ans^=tmp;
        pre=tmp;
    }
    printf("%lld\n",ans);
	return 0;
} 
```
---
title: Codeforces Manthan18 题解
date: 2018-09-04 00:13:09
tags:
- Codeforces
- Solution
- Graph
categories:
- Codeforces
---

# A Packets

将n枚硬币分成几块，要求能够每次拿出几块组成任意1到n的整数，块数最小。

考虑多重背包的二进制优化，计算n的二进制位数即可。

# B Reach Median

给一个序列，每次对一个数加1或减1，求使得中位数等于x的最小操作数。

将原序列排序，二分找到第一个大于x的位置，如果在中位数后则这一部分全部加成中位数，反之亦然。

# C Equalize

给两个01串，对第一个串操作，交换两个位置，花费是下标之差的绝对值，翻转一个位置，花费是1。

显然除非两个错误位置时相邻的不同数，否则都使用翻转操作。

将原序列处理成一个012序列，0表示位置正确，12表示错误，1表示错位的是0，2表示错位的是1。

第一遍扫一下这个序列，把相邻的12取出来，第二遍扫一下这个序列加上剩余的12个数。

# D Valid BFS?

给一棵树，判断给的序列是否为合法的BFS序列。

合法BFS序列第一个一定是1，然后直接遍历给定的序列。

记录一下每次遍历的一个点时，他的子节点的位置应该在哪。

使用set对比真正的子节点和序列中的子节点。

更新子节点位置，也就是加上当前点的子节点个数。

# E Trips

n个人，m天，每天加一条边，询问一次，当天加边之后，在图中选取一个子图，这些点度数都大于k，输出这个子图的最大大小。

先考虑一个静态的问题，m条边全部给定，那么考虑迭代，直到图上剩余点度数都大于等于k，每次迭代将所有度数小于k的点删除，连接他的边也删除。

优化一下迭代过程，考虑维护一个队列，记录所有度数小于k的点，遍历这个队列，每次遍历到一个点时进行删点和删边，如果使得一个点度数恰好小于k就入队（防止重复入队）。

回到原题，可以反过来处理，变加边为删边，建好整个图之后，每次当天加的边没有删除，就把他删除。

算法复杂度来源于第一次遍历原图。

<!--more-->

# 代码

### A Packets

{% codeblock lang:cpp %}
#include <cstdio>
#include <cstring>
#include <algorithm>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000 + 5;

ll n;

int main(){
    scanf("%I64d", &n);
    int ans = 0;
    for (int i = 0; i < 33; i++){
        if (n > (1ll << i)) n -= (1ll << i), ans++;
        else break;
    }
    if (n) ans++;
    printf("%d", ans); 
    return 0;
}
{% endcodeblock %}

### B Reach Median

{% codeblock lang:cpp %}
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <cmath>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 200000 + 5;

int n, s, a[maxn];

int main(){
    scanf("%d%d", &n, &s);
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    sort(a, a + n);
    int k = upper_bound(a, a + n, s) - a;
    ll ans = 0;
    // printf("%d\n", k);
    if (k > n / 2){
        for (int i = n / 2; i < k; i++){
            ans += 1ll * abs(s - a[i]);
        }
    }
    else{
        for (int i = k; i <= n / 2; i++){
            ans += 1ll * abs(a[i] - s);
        }
    }
    printf("%I64d", ans);
    return 0;
}
{% endcodeblock %}

### C Equalize

{% codeblock lang:cpp %}
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <cmath>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 1000000 + 5;

char a[maxn], b[maxn];
int n, vis[maxn];
vector<int> s[2];

int main(){
    scanf("%d%s%s", &n, a, b);
    for (int i = 0; i < n; i++){
        if (a[i] != b[i]) /* s[a[i] - '0'].push_back(i); */
            vis[i] = a[i] - '0' + 1;
    }
    int ans = 0, tot = 0;
    for (int i = 1; i < n; i++){
        if (vis[i] != 0 && vis[i - 1] != 0 && vis[i] != vis[i - 1]){
            ans++; vis[i] = vis[i - 1] = 0;
        }
    }
    for (int i = 0; i < n; i++) if (vis[i]) ans++;
    printf("%d", ans);
    return 0;
}
{% endcodeblock %}

### D Valid BFS?

{% codeblock lang:cpp %}
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>
#include <set>
#include <vector>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 200000 + 5;

vector<int> edge[maxn];
int n, a[maxn], vis[maxn];

int bfs(){
    vis[1] = 1;
    int l = 0, s = 1; 
    while (l < s){
        int t = a[l];
        set<int> p, q;
        for (int v : edge[t]) if (!vis[v]) p.insert(v);
        for (int i = s; i < s + p.size(); i++) q.insert(a[i]);
        if (p != q) return 0;
        for (int i : p) vis[i] = 1;
        s += p.size(); l++; 
    }
    return 1;
}

int main(){
    scanf("%d", &n);
    for (int i = 0, x, y; i < n - 1; i++){
        scanf("%d%d", &x, &y); 
        edge[x].push_back(y); edge[y].push_back(x);
    }
    for (int i = 0; i < n; i++) scanf("%d", a + i);
    if (a[0] != 1) {puts("No"); return 0;}
    if (bfs()) puts("Yes");
    else puts("No");
    return 0;
}
{% endcodeblock %}

### E Trips

{% codeblock lang:cpp %}
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <queue>
#include <utility>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
typedef pair<int, int> PII;
const int maxn = 200000 + 5;

vector<int> q;
vector<PII> edge[maxn];
int from[maxn], to[maxn], deg[maxn], vis[maxn];
int n, m, k, ans[maxn];

int main(){
    scanf("%d%d%d", &n, &m, &k);
    for (int i = 0; i < m; i++){
        scanf("%d%d", from + i, to + i);
        deg[from[i]]++; deg[to[i]]++;
        edge[from[i]].emplace_back(to[i], i);
        edge[to[i]].emplace_back(from[i], i);
    }
    for (int i = 1; i <= n; i++){
        if (deg[i] < k){
            q.push_back(i);
        }
    }
    int l = 0;
    for (int i = m - 1; i >= 0; i--){
        while (l < q.size()){
            int t = q[l];
            for (PII v : edge[t]){
                if (vis[v.second]) continue;
                vis[v.second] = 1;
                deg[v.first]--; deg[t]--;
                if (deg[v.first] == k - 1) q.push_back(v.first);
            }
            l++;
        }
        ans[i] = n - q.size();
        if (vis[i]) continue;
        vis[i] = 1; deg[from[i]]--; deg[to[i]]--;
        if (deg[from[i]] == k - 1) q.push_back(from[i]);
        if (deg[to[i]] == k - 1) q.push_back(to[i]);
    }
    for (int i = 0; i < m; i++) printf("%d\n", ans[i]);
    return 0;
}
{% endcodeblock %}
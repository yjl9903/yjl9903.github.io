---
mathjax: true
title: 最大团
date: 2019-05-29 21:19:21
tags:
- Graph
categories:
- 图论
---

# 最大团

编号从 $0$ 开始。 

```c++
struct Max_Clique {
    static const int N = 100;
    vector<int> sol;
    int el[N][N / 30 + 1], s[N][N / 30 + 1];
    int n, ans, dp[N];
    void init(int _n) {
        n = _n;
        for(int i = 0; i <= n; i++) {
            dp[i] = 0;
            ms(el[i], 0);
        }
    }
    /* Zero Base */
    void add_edge(int a,int b) {
        if(a > b) swap(a, b);
        if(a == b) return;
        el[a][b / 32] |= (1 << (b % 32));
    }
    bool dfs(int x,int k) {
        int c = 0, d = 0;
        for(int i = 0; i < (n + 31) / 32; i++) {
            s[k][i] = el[x][i];
            if (k != 1) s[k][i] &= s[k - 1][i];
            c += __builtin_popcount(s[k][i]);
        }
        if (c == 0) {
            if(k > ans) {
                ans = k;
                sol.clear();
                sol.push_back(x);
                return 1;
            }
            return 0;
        }
        for (int i = 0; i < (n + 31) / 32; i++) {
            for (int a = s[k][i]; a; d++) {
                if (k + (c - d) <= ans) return 0;
                int lb = a & (-a), lg = 0;
                a ^= lb;
                while (lb!=1) {
                    lb = (unsigned int)(lb) >> 1;
                    lg++;
                }
                int u = i * 32 + lg;
                if (k + dp[u] <= ans) return 0;
                if (dfs(u, k + 1)) {
                    sol.push_back(x);
                    return 1;
                }
            }
        }
        return 0;
    }
    int solve() {
        ans = 0;
        for(int i = n - 1; i >= 0; i--) {
            dfs(i,1);
            dp[i] = ans;
        }
        return ans;
    }
} mcp;
```

<!--more-->

# 最大独立集

随机化。

```c++
const int maxn = 100 + 5;

int n, m, p[maxn], vis[maxn], G[maxn][maxn];

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1, u, v; i <= m; i++) {
        scanf("%d%d", &u, &v);
        G[u][v] = G[v][u] = 1;
    }
    for (int i = 1; i <= n; i++) p[i] = i;
    int T = 10000, ans = 0;
    vector<int> rec;
    while (T--) {
        random_shuffle(p + 1, p + 1 + n);
        ms(vis, 0);
        vector<int> v; 
        int res = 1; vis[1] = 1;
        v.push_back(p[1]);
        for (int i = 2; i <= n; i++) {
            int flag = 1;
            for (int j = 1; j < i; j++) {
                if (vis[j] && G[p[i]][p[j]]) {
                    flag = 0; break;
                }
            }
            if (flag) {
                v.push_back(p[i]);
                vis[i] = 1; res++;
            }
        }
        if (res > ans) {
            ans = res; rec = v;
        }
    }
    printf("%d\n", ans);
    for (int i = 0; i < (int)rec.size(); i++) {
        if (i) putchar(' ');
        printf("%d", rec[i]);
    }
    return 0;
}
```
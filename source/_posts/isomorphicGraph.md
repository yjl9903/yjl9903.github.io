---
title: 同构图的判定
date: 2018-08-16 09:34:09
tags: Graph
categories: 图论
comments: false
---

枚举映射。

{% codeblock lang:cpp %}
struct edge{int x, y;};
using G = vector<edge>;
 
inline G read(int m){
    G a;
    for (int i = 0; i < m; i++){
        int x, y; scanf("%d%d", &x, &y);
        a.push_back(edge {x, y});
    }
    return a;
}
inline int count(const G& b, const G& a){
    ms(vis, 0);
    for (int i = 0; i < a.size(); i++) vis[a[i].x][a[i].y] = vis[a[i].y][a[i].x] = 1;
    for (int i = 0; i <= n; i++) f[i] = i;
    int res = 0;
    do{
        bool flag = true;
        for (int i = 0; i < b.size(); i++){
            if (!vis[f[b[i].x]][f[b[i].y]]){
                flag = false; break;
            }
        }
        if (flag) res++;
    } while (next_permutation(f + 1, f + 1 + n));
    return res;
}
{% endcodeblock %}
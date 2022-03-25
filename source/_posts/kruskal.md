---
title: Kruskal
date: 2018-08-18 10:47:34
tags: [Graph]
categories: 图论
comments: false
---

{% codeblock lang:cpp %}
struct edge{char x, y; int d;};
bool cmp(const edge& a, const edge& b){
    return a.d < b.d;
}
vector<edge> v;

int n, pre[maxn];
void init(){
    for (int i = 0; i < maxn; i++) pre[i] = i;
}
int find(int x){return x == pre[x] ? x : pre[x] = find(pre[x]);}
void join(int x, int y){
    x = find(x), y = find(y);
    pre[x] = y;
}
int kruskal(){
    sort(v.begin(), v.end(), cmp);
    int ans = 0;
    for (int i = 0, a, b; i < v.size(); i++){
        a = find(v[i].x - 'A'), b = find(v[i].y - 'A');
        if (a == b) continue;
        join(a, b); ans += v[i].d;
    }   
    return ans;
}
{% endcodeblock %}
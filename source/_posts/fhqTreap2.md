---
title: 无旋Treap 区间翻转
date: 2018-08-16 15:10:47
tags: [Data Structure, Treap]
categories:
- 数据结构 
- 平衡树
comments: false
---

{% codeblock lang:cpp %}
struct fhqTreap{
    int ch[maxn][2], size[maxn], key[maxn], rnd[maxn], rev[maxn], root, tot;
    fhqTreap(){ root = tot = 0; }
    void clear(){ root = tot = 0; }
    int node(int v, int x){
        key[++tot] = v; rnd[tot] = rand();
        size[tot] = 1; ch[tot][0] = ch[tot][1] = rev[tot] = 0;
        return tot;
    }
    void pushup(int x){size[x] = size[ch[x][0]] + size[ch[x][1]] + 1;}
    void pushdown(int x){
        if (!rev[x]) return;
        swap(ch[x][0], ch[x][1]);
        if (ch[x][0]) rev[ch[x][0]] ^= 1; 
        if (ch[x][1]) rev[ch[x][1]] ^= 1;
        rev[x] = 0;
    }
    void split(int now, int k, int &x, int &y){
        if (!now) {
            x = y = 0; return;
        }
        pushdown(now);
        if (size[ch[now][0]] < k){
            x = now; 
            split(ch[now][1], k - size[ch[now][0]] - 1, ch[now][1], y);
        }
        else {
            y = now; 
            split(ch[now][0], k, x, ch[now][0]);
        }
        pushup(now);
    }
    int merge(int x, int y){
        if (!x || !y) return x + y;
        if (rnd[x] < rnd[y]){
            pushdown(x);
            ch[x][1] = merge(ch[x][1], y);
            pushup(x); return x;
        }
        else {
            pushdown(y);
            ch[y][0] = merge(x, ch[y][0]);
            pushup(y); return y;
        }
    }
    void reverse(int l, int r){
        int x, y, z;
        split(root, l - 1, x, y);
        split(y, r - l + 1, y, z);
        rev[y] ^= 1;
        root = merge(x, merge(y, z));
    }
    void print(int x){
        if (!x) return;
        pushdown(x);
        print(ch[x][0]);
        v.push_back(key[x]);
        print(ch[x][1]);
    }
}f;
{% endcodeblock %}

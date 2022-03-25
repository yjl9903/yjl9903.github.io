---
title: 伸展树
date: 2018-08-07 22:59:22
tags: [Data Structure, Splay]
categories: 
- 数据结构 
- 平衡树
comments: false
---

cnt[x] 表示 x 的出现次数，size[x] 表示以 x 为根节点的子树大小。

- ## clean(int x) 

- 清除当前节点

- ## get(int x) 

- 获取当前节点是父节点的左右孩子

- ## update(int x) 

- 更新当前节点的 size 

- ## rot(int x)

- 将当前节点向上旋转

- ## splay(int x)

- 将当前节点伸展为根节点

- ## insert(int v)

- 创建一个新的节点，并将其旋转至根

- ## find(int v)

- 返回键为 v 的节点的名次，并将其旋转至根节点

- ## findx(int x)

- 返回排名为 x 的键

- ## prev()

- 返回根节点的前驱

- ## succ()

- 返回根节点的后继

- ## del(int v)

- 删除一个键为 v 的节点

# 模板:

{% codeblock lang:c %}
struct Splay{
    int f[maxn], ch[maxn][2], key[maxn], cnt[maxn], size[maxn], sz = 0, root = 0;
    Splay(){sz = root = 0;}
    void clear(int x){
        ch[x][0] = ch[x][1] = f[x] = key[x] = cnt[x] = size[x] = 0;
    }
    int get(int x){
        return ch[f[x]][1] == x;
    }
    void update(int x){
        if (!x) return;
        size[x] = cnt[x];
        if (ch[x][0]) size[x] += size[ch[x][0]];
        if (ch[x][1]) size[x] += size[ch[x][1]];
    }
    void rot(int x){
        int old = f[x], oldf = f[old], tp = get(x);
        ch[old][tp] = ch[x][tp ^ 1]; f[ch[old][tp]] = old;
        f[old] = x; ch[x][tp ^ 1] = old;
        f[x] = oldf;
        if (oldf) ch[oldf][ch[oldf][1] == old] = x;
        update(old); update(x);
    }
    void splay(int x){
        for (int fa; fa = f[x]; rot(x))
            if (f[fa]) rot(get(x) == get(fa) ? fa : x);
        root = x;
    }
    int insert(int v){
        if (root == 0){
            sz++; ch[sz][0] = ch[sz][1] = f[sz] = 0;
            key[sz] = v; cnt[sz] = 1; size[sz] = 1; 
            root = sz;
            return 1;
        }
        int tot = root, fa = 0;
        while (1){
            if (key[tot] == v){
                cnt[tot]++; update(tot); update(fa);
                splay(tot);
                return cnt[tot];
            }
            fa = tot;
            tot = ch[tot][v > key[tot]];
            if (tot == 0){
                sz++; ch[sz][0] = ch[sz][1] = 0; 
                key[sz] = v; cnt[sz] = size[sz] = 1;
                f[sz] = fa; ch[fa][v > key[fa]] = sz;
                update(fa); splay(sz);
                break;
            }
        }
        return 1;
    }
    int find(int v){
        int ans = 0, tot = root;
        while (1){
            if (v < key[tot]) tot = ch[tot][0];
            else {
                ans += (ch[tot][0] ? size[ch[tot][0]] : 0);
                if (v == key[tot]){
                    splay(tot); return ans + 1;
                }
                ans += cnt[tot];
                tot = ch[tot][1];
            } 
        }
        return 0;
    }
    int findx(int x){
        int tot = root;
        while (1){
            if (ch[tot][0] && x <= size[ch[tot][0]]) tot = ch[tot][0];
            else {
                int t = (ch[tot][0] ? size[ch[tot][0]] : 0) + cnt[tot];
                if (x <= t) return key[tot];
                x -= t; tot = ch[tot][1];
            }
        }
        return -1;
    }
    int prev(){
        int tot = ch[root][0];
        while (ch[tot][1]) tot = ch[tot][1];
        return tot;
    }
    int succ(){
        int tot = ch[root][1];
        while (ch[tot][0]) tot = ch[tot][0];
        return tot;
    }
    void del(int v){
        find(v);
        if (cnt[root] > 1){
            cnt[root]--; return;
        }
        if (!ch[root][0] && !ch[root][1]){
            clean(root); root = 0; return;
        }
        if (!ch[root][0]){
            int old = root; root = ch[root][1]; f[root] = 0;
            clean(old); return;
        }
        else if (!ch[root][1]){
            int old = root; root = ch[root][0]; f[root] = 0;
            clean(old); return;
        }
        int lf = prev(), old = root;
        splay(lf); f[ch[old][1]] = root; ch[root][1] = ch[old][1];
        clean(old); update(root);
    }
};
{% endcodeblock %}

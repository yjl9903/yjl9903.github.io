---
mathjax: true
title: 树论
date: 2019-10-03 23:24:35
tags:
  - Tree
categories:
  - 树
---

# 树论

在算法竞赛中，树是一种应用广泛的抽象数据类型或者实现抽象数据类型的数据结构。

树结构可能会出现各种各样的题目中，作为解决问题算法或者问题本身。

> 线段树 / 树状数组维护 ······ 即可！
>
> 给一棵树，······，求 ······！

树论主要讨论在具体的一般树上的算法和问题。

![](https://github.com/yjl9903/Pictures/raw/master/Tree/tree.png)

<!--more-->

## 树基础

图论中的树和现实生活中的树长得一样，只不过我们习惯于处理问题的时候把树根放到上方来考虑。

这种数据结构看起来像是一个倒挂的树，因此得名。

### 无根树的等价定义

一个没有固定根结点的树成为无根树 (unrooted tree)。

+ 有 $n$ 个点，$n-1$ 条边的连通无向图
+ 无向无环的连通图
+ 任意两个结点之间有且仅有一条简单路径的无向图
+ 任何边均为桥的连通图
+ 没有环，且在任意不同两点间添加一条边之后所得图含唯一的一个环的图

在无根树的基础上，指定一个结点为根，则形成了一棵有根树。

在解决无根树上的问题时，我们通常也会为他指定一个根，方便问题的处理。

### 有关树的定义

+ 森林 **(Forest)** ：每一个连通块都是树的图（一棵树也是一个森林)
+ 生成树 **(Spanning Tree)** ：一个连通无向图的生成子图，同时要求是树
+ 结点的深度 **(depth)** ：到根结点的路径上的边数
+ 树的高度 **(height)** ：所有结点深度的最大值
+ 无根树的叶子 **(leaf node)** ：度数**不超过** $1$ 的结点
+ 有根树的叶子 **(leaf node)** ：没有子结点的结点

### 有根树的定义

+ 父亲 **(parent node)**：对于除根以外的每个结点，定义为从该结点到根路径上的第二个结点。 根结点没有父结点。
+ 祖先 **(ancestor)**：一个结点到根结点的路径上，除了它本身外的结点。 根结点的祖先集合为空。
+ 子结点 **(child node)**：如果 $u$ 是 $v$ 的父亲，那么 $v$ 是 $u$ 的子结点。
+ 兄弟 **(sibling)**：同一个父亲的多个子结点互为兄弟。
+ 后代 **(descendant)**：如果 $u$ 是 $v$ 的祖先，那么 $v$ 是 $u$ 的后代。
+ 子树 **(subtree)**：删掉与父亲相连的边后，该结点所在的子图（所有到根结点路径上经过 $u$ 的结点形成子图)。

![](https://github.com/yjl9903/Pictures/raw/master/Tree/tree-basic.png)

### 特殊的树

+ 链 **(chain)**：满足与任一结点相连的边不超过 $2$ 条的树称为链。

  ![](https://github.com/yjl9903/Pictures/raw/master/Tree/chain.png)

+ 菊花 / 星星 **(star)**：满足存在 $u$ 使得所有除 $u$ 以外结点均与 $u$ 相连的树称为菊花。

  ![](https://github.com/yjl9903/Pictures/raw/master/Tree/star.png)

#### 例题

交互题，有一棵 $n$ 个结点的满 $k$ 叉树，标号 $1$ 到 $n$，你现在需要用 $60n$ 次询问猜出这棵树的根。

每次你可以询问树上从 $a$ 到 $b$ 的路径上是否经过结点 $c$（$1 \le a,b,c \le n$）。

其中 $3 \le n \le 1500$，$2 \le k < n$。

来源：[Codeforces Round #523 F Last Root](https://codeforces.com/contest/1061/problem/F)

可以实现 $3$ 件事：

1. 给定路径的端点 $u,v$，我们可以用 $n$ 次询问找到路径上所有点
2. 给定路径的端点 $u,v$，我们可以询问 $a$，$a$ 到 $v$ 的路径是否有 $u$，不断调整，可以用 $2n$ 次询问将 $u,v$ 转移到两个不同叶子。
3. 对于一条叶子和叶子之间路径，如果路径长度为 $2\times height-1$，那么这条路径必定经过根，对于根结点，恰好有 $dep-2$ 个结点分别在到两个叶子中间，询问次数 $\log^2 n$。

总结一下，我们需要选定一组初始的 $u,v$ ，目标是获得一条经过根结点的路径，注意到一个事实，如果我们选了一个点，再选一个点使得两点之间路径经过根结点的概率是 $k-1\over k$，因此随机两点即可。

### 树的实现

> 便于后面的讲解，如果不加特别说明，默认树是一棵一般树，点数的数量级至少为 $10^5$，需要使用线性或线性对数时间复杂度的算法。边权和点权范围默认为 $10^9$。

一般树和一般无向图储存方式是相同的，主要使用邻接表进行实现。

> 使用邻接矩阵实现的树在时空上都无法接受

#### 链式前向星

##### 数据结构定义

```c++
struct edge {
  int to, nxt; // other information
} g[maxn * 2]; // 无向边
int head[maxn], tot;
```

##### 加边

```c++
void adde(int u, int v) {
  g[++tot] = (edge){v, head[u]}; head[u] = tot;
}
adde(u, v); adde(v, u);
```

##### dfs

```c++
void dfs(int u, int f) {
  // ...
  for (int i = head[u]; i; i = g[i].nxt) {
    int u = g[i].to;
    if (v == f) continue;
    // ...
    dfs(v, u);
    // ...
  }
  // ...
}

// 空结点：0，根：1
dfs(1, 0)；
```

#### vector

##### 数据结构定义

```c++
vector<int> edge[maxn];
vector<pair<int,int>> edge[maxn]; // other information
```

##### 加边

```c++
edge[u].push_back(v);
edge[v].push_back(u);
```

##### dfs

```c++
void dfs(int u, int f) {
  // ...
  for (int& v: edge[u]) {
    if (v == f) continue;
    // ...
    dfs(v, u);
    // ...
  }
  // ...
}
```

## dfs序

$dfs$ 序是在深度优先遍历的过程中，进入每个结点的时间戳，即记录第几个访问到的结点是什么。

![](https://github.com/yjl9903/Pictures/raw/master/Tree/dfsorder.png)

| 时间戳 |  1   |  2   |  3   |  4   |  5   |
| :----: | :--: | :--: | :--: | :--: | :--: |
|  结点  |  A   |  B   |  D   |  E   |  C   |

$dfs$ 序有一个很好的性质是一棵子树内的所有结点是 $dfs$ 序上的一段连续区间。

### 参考代码

```c++
int in[maxn], out[maxn], tot = 0;
void dfs(int u, int f) {
  in[u] = ++tot;
  for (int& v: edge[u]) {
    if (v == f) continue;
    dfs(v, u);
  }
  out[u] = tot;
}
```

### 例题1

给定一棵 $n$ 个点以 $1$ 为根的有根树，有 $q$ 次询问，每次询问点 $u$ 的子树内，进行深度优先遍历时，进栈的第 $k$ 个点是什么，如果没有第 $k$ 个点输出 $-1$。每个点 $dfs$ 时都是按照儿子的编号大小顺序依次进行遍历。

其中 $2 \le n \le 2 \times 10^5,1\le q \le 2\times10^5,1\le u,k \le n$。

来源：[Codeforces Round #498 E. Military Problem](https://codeforces.com/contest/1006/problem/E)

发现 $dfs$ 序已经给定，预处理出 $dfs$ 序和每个点进出的时间戳，回答 $dfs$ 序上 $u$ 的进入时间后的第 $k$ 个。

### 例题2

给定 $n$ 个结点的无根树，你现在要把它画在一个平面上，使得边不相交，并且所有点的坐标满足 $1\le x_i,y_i \le n$。

其中 $1 \le n \le 1000$。

来源：[CCPC-Wannafly Winter Camp Day5 (Div. 2) A. Cactus Draw](https://www.zhixincode.com/contest/23/problem/A?problem_id=326)

记 $u$ 的 $dfs$ 序时间戳为 $dfn[u]$，深度为 $dep[u]$，那么 $u$ 的坐标为 $(dfn[u],dep[u])$。

### 经典问题

有了 $dfs$ 序，我们实际上是将一棵树拍平成了一个序列，于是一些原本在序列上的问题就可以放到树上来做。

> 给定一棵 $n$ 个点的有根树，每个点有一个权值，$q$ 次操作，有两种操作：
>
> 1. 将 $u$ 的子树内所有点的权值加 $x$
> 2. 询问 $u$ 的子树内所有点的权值和
>
> $1 \le n,q \le 10^5$

树上子树更新，查询子树信息。

根据我们上面的性质，我们可以记录一下每个点进栈时的时间戳 $in$ 和出栈时的时间戳 $out$，那么一个点的子树内所有点对应 $dfs$ 序上 $in$ 到 $out$ 的连续区间。于是，问题就是区间修改和区间查询，这个问题可以使用线段树进行解决。

类似的，只要是序列上可以使用线段树（主席树）等类似数据结构维护区间操作的问题，都可以放到树上进行，甚至树上的问题会比序列上的问题更容易，因为 $dfs$ 序的性质，我们操作的区间要么是包含关系，要么没有交集。

> [第十届南京理工大学程序设计大赛 E 题](http://icpc.njust.edu.cn/Contest/6245/E/) 
>
> 主席树询问区间第 $k$ 大

#### 例题

给定一棵 $n$ 个点以 $1$ 为根的有根树，保证每个结点的编号与其 $dfs$ 序的编号相同（每个点 $dfs$ 时都是按照儿子的编号大小顺序依次进行遍历）。$q$ 次询问在编号 $[l,r]$ 区间内距离 $v$ 最近的叶子结点的距离。

其中 $3 \le n \le 5 \cdot 10^5,1 \le q \le 5 \cdot 10^5$。

来源：[Codeforces Global Round 1 F. Nearest Leaf](https://codeforces.com/contest/1110/problem/F)

离线所有询问到每个点上，动态维护一棵线段树表示所有点到当前 $dfs$ 的点的距离。考虑 $dfs$ 转移到儿子结点 $v$ 时，相当于子树 $v$ 内所有叶子的距离减去这条边的长度，子树 $v$ 外所有叶子的距离加上这条边的长度。

### 例题3

给定一棵 $n$ 个结点的有根树，维护两个操作：

1. 所有深度为 $l$ 的点权值加上 $x$
2. 询问子树的权值和

其中 $1\le n,q \le 10^5,1\le x\le 10^9$。

来源：[ACM-ICPC 2018 沈阳赛区网络赛 J. Ka Chang](https://nanti.jisuanke.com/t/A1998)

对于一次修改操作，有两个处理思路：

1. 树状数组维护，暴力修改所有深度为 $l$ 的单点，时间复杂度：$O(n \log n) $
2. 在深度 $l$ 上打标记，预处理 $dfs$ 序深度为 $d$ 的个数的前缀和，时间和空间复杂度：$O(n^2) $，回答询问的时间复杂度为 $O(n)$。

可以注意到，前一个算法可以处理点数少的情况，后一个适用于点数较多的情况，考虑设置一个阈值 $T$。

当深度为 $l$ 的点数小于 $T$ 时，应用前一个算法，当点数大于 $T$ 时，这样的深度最多有 $n \over T$ 种，应用后一个算法。

单次询问，前一个算法的时间复杂度 $O(T \log n)$，后一个算法时间复杂度 $O({n \over T})$。那么总的时间复杂度，由均值不等式，$T=\sqrt{n\over \log n}$ 时取最小值，则总体时间复杂度 $O(q\sqrt{n \log n}) $。

### 例题4

给定一棵 $n$ 个结点的有根树，进行 $q$ 次操作，每次操作将结点 $u$ 的子树中，距离 $u$ 小于等于 $k$ （距离为路径上的边数）的子联通块中所有点权值加 $x$，求 $q$ 次操作后每个点的权值。

其中 $1 \le n,q \le 3 \cdot 10^5,  1 \le k,x\le 10^9$。

来源：[Educational Codeforces Round 54  E. Vasya and a Tree](https://codeforces.com/contest/1076)

将所有操作离线到每个结点上，我们可以发现每个结点的权值，只和它的所有祖先上的操作有关。$dfs$ 时，栈中的结点就是当前遍历的点到根的路径，而操作就是 $dep[u]$ 到 $dep[u]+k$ 这一段深度区间上加 $x$，单点查询某个点的权值，进出栈时用树状数组维护操作即可。

### 例题5

给定一棵 $n$ 个结点带点权 $w_i$ 的有根树，将树划分成满足条件的垂直路径，路径上的点数小于等于 $L$，路径上的点权和小于等于 $S$，垂直路径为一个结点序列 $v_1,v_2,\dots,v_k$，满足 $v_i(2\le i \le k)$ 是 $v_{i-1}$ 的父亲。每个点都必须属于一个垂直路径内，求最少能分割成多少个这样的路径。

其中 $1 \le n \le 10^5,1\le w_i \le 10^9,1\le L \le 10^5,1\le S \le 10^{18}$。

来源：[Codeforces Round #514 E. Split the Tree](https://codeforces.com/problemset/problem/1059/E)

题目路径的条件比较特殊，不会出现在某个点分叉的情况，因此，每个叶子结点都必须在一个垂直路径中，考虑一个贪心的做法，对于任何一个点，在满足限制的条件下尽量往上爬，这样肯定是优的。但是，会出现多个叶子的路径重叠的情况，这是不影响答案的，只需要在重合位置断掉一条路径即可，并且显然没有办法把一条路径优化掉。

于是，我们可以从叶子往上爬，标记一下每个点能往上爬到的最远位置。如果一个点 $u$ 的所有儿子都不能把某一条路径延伸到 $u$ 上，那么必须要从 $u$ 开始一条新的路径。

现在，我们的问题是当需要新开一条路径时，我们如何获得它最远能爬到什么位置。考虑在 $dfs$ 栈上维护一个前缀和，表示每个点到根的距离，这个栈上的前缀和显然有单调性，因此可以在上面二分找到深度最小的点，满足到当前点的路径上点权和小于等于 $S$。

## 最近公共祖先

最近公共祖先简称 LCA (Lowest Common Ancestor)。

LCA 是树上的一个经典问题，也是解决很多树上问题的重要理论依据和解决工具。

两个结点的最近公共祖先，就是两个点的公共祖先中深度最深的那个。

### 朴素

1. 将一个点往父亲跳到根，记录一下经过了哪些点，在把另外一个点往上跳，第一个相遇的点就是最近公共祖先。显然一条链情况下，单次查询复杂度 $O(n)$。
2. 先调整深度大的结点，令他们深度相同，因此他们到 $LCA$ 的距离相同，所以可以两个点一起往上跳，第一个相遇的点就是最近公共祖先，单次查询复杂度 $O(n)$。

![](https://github.com/yjl9903/Pictures/raw/master/Tree/bz.png)

> 上图，10 和 15 的最近公共祖先为 1，第一步将 10 跳转到 7，第二步 7 和 15 一起跳转到 1。

### 倍增

倍增算法是最经典的 $LCA$ 求法，他改进了第二个朴素算法。

我们可以看到朴素算法的瓶颈在于如何快速地向根进行跳转。朴素算法中每次跳转只能往上跳一步，我们考虑让结点跳转时能够多跳几步。但是，在第一个过程，我们不可能开一个数组记录下所有点往上跳几步是哪个点，这样空间复杂度是 $O(n^2)$ 的；在第二个过程中，如果每次跳了超过一步，那么可能相遇位置不是 $LCA$。

我们预处理出每个结点 $x$ 的第 $2^i$ 个祖先 `fa[x][i]`。然后，我们每次就可以在 $O(\log h)$ 的时间内将任意一个点往上跳 $h$ 步。

我们知道，$h$ 拆成二进制数的位数是 $O(\log h)$ 的。于是，将 $h$ 二进制分解，例如 $h=(5)_{10}=(101)_{2}$，这意味着我们需要将 $x$ 往上跳转 $2^0,2^2$ 次，即 $x \rightarrow  fa[x][0]$， $x \rightarrow fa[x][2]$。

查询两个点 $u,v$ 的 $LCA$ 时，第一步将深度较大的结点 $u$ 往上跳 $dep[u]-dep[v]$ 步，如果两个点有祖孙关系，这步两个点已经重合，返回答案。

否则，我们需要将两个点一起往上跳，但是这时我们不知道需要跳多少步才行。于是，可以想到二分，找到最大的深度差，使得两个点跳转上去仍然是不同的点，这是以 $LCA$ 为界的。如果我们跳 $2^i$ 步到了一个相同的祖先上，那么距离最近公共祖先的距离一定小于等于 $2^i$ ，要么恰好是距离 $2^i$，要么可以拆分成 $2^{i-1}+\dots$ ，于是我们再尝试跳转 $2^{i-1}$ 步，以此类推，一定可以跳到两个点的父亲相同。这个过程实际上也等价于二分。答案就是 $fa[u][0]$ 或 $fa[v][0]$。

那么现在问题是如何预处理出 `fa[x][i]`。我们首先需要知道每个结点的父亲是什么，即 `fa[x][0]`。然后对于 $2^i(i>0)$，有这样的 $dp$ 转移方程
$$
fa[x][i]=fa[fa[x][i-1]][i-1]
$$
也就是，结点 $x$ 的第 $2^i$ 个祖先是 $x$ 的第 $2^{i-1}$ 个祖先的 $2^{i-1}$ 个祖先。

预处理的时间复杂度为 $O(n \log n)$，空间复杂度 $O(n \log n)$。

单次询问的时间复杂度 $O(\log n)$。

#### 参考代码

```c++
namespace LCA {
    int fa[maxn][20], dep[maxn];
    void dfs(int u, int f) {
        fa[u][0] = f; dep[u] = dep[f] + 1;
        for (auto& x: edge[u]) {
            int v = x.first;
            if (v == f) continue;
            dfs(v, u);
        }
    }
    void init() {
        dfs(1, 0);
        for (int j = 1; j < 20; j++) for (int i = 1; i <= n; i++) 
            fa[i][j] = fa[fa[i][j - 1]][j - 1];
    }
    int qlca(int u, int v) {
        if (dep[u] < dep[v]) swap(u, v);
        int tmp = dep[u] - dep[v];
        for (int i = 0; tmp; i++, tmp >>= 1) 
            if (tmp & 1) 
                u = fa[u][i];
        if (u == v) return u;
        for (int i = 19; i >= 0; i--) 
            if (fa[u][i] != fa[v][i]) 
                u = fa[u][i], v = fa[v][i];
        return fa[u][0];
    }
}
```

### 应用

#### 路径长度

$LCA$ 的一个重要应用就是可以计算树上两点之间路径长度。

在算法竞赛中，一个很重要的思路是前缀和思想。在序列 $a$ 上，如果我们预处理出所有 $\sum_{i=1}^k a_i(1\le k\le n)$，那么就可以 $O(1)$ 的时间内，回答任意一个区间的和 $\sum_{i=l}^r a_i=\sum_{i=1}^{r} a_i -\sum_{i=1}^{l-1}a_i$。

我们可以把序列也看成一棵树，即一条链，区间求和代表端点之间的距离，上面这件事相应的也可以放到一般树上来做，同样预处理出所有点到根的路径长度。观察一下， $4$ 和 $10$ 之间的路径，可以拆开看成 $3$ 个部分，$1$ 到 $4$ 的路径，$1$ 到 $10$ 的路径，$1$ 到 $2$ 的路径，$2$ 是路径上的拐点，其实也是 $4$ 和 $10$ 的最近公共祖先。

记 $path(u,v)$ 表示 $u$ 到 $v$ 的路径长度。如下图， $path(4,10)=path(1,4)+path(1,10)-2\cdot path(1,2)$。

一般地，$path(u,v)=path(root,u)+path(root,v)-2\cdot path(root,LCA(u,v))$，我们将任意路径长度的计算全部都转移到了到根的距离，这部分可以预处理出来。应用倍增算法可以在 $O(\log n)$ 的时间内找到最近公共祖先，于是就可以相应的快速得到树上任意两点之间的距离。

![](https://github.com/yjl9903/Pictures/raw/master/Tree/lcapath.png)

##### 例题1

给定一棵 $n$ 个点 $n$ 条带边权的无向联通图，$q$ 次操作，修改一条边的边权和询问任意两点之间的最短路径长度。

其中 $1 \le n,q \le 10^5$。

来源：[2018 HDu Multi-University Training Contest 7 H. Traffic Network in Numazu](http://acm.hdu.edu.cn/showproblem.php?pid=6393)

$n$ 个点 $n$ 条边的无向连通图，而一棵 $n$ 个点的树有 $n-1$ 条边，现在是在一棵树上加了一条环边。

询问时，考虑将一条环边断开，每次询问拆成两部分考虑，不走环边和走环边，取最小值。

考虑修改操作的贡献，实际上是修改了这条边结点的子树内所有点到根的距离，树状数组维护区间加减和单点查询即可。

##### 例题2

给定一个 $n$ 个点 $m$ 条边的带边权无向连通图，$q$ 次询问 $u$ 和 $v$ 之间的最短路径长度。

其中 $1 \le n,q \le 10^5,n-1\le m \le n+20$。

来源：[Educational Codeforces Round 51 F. The Shortest Statement](https://codeforces.com/contest/1051/problem/F)

乍看一下题目并不可做，但数据范围很神奇。

这个无向联通图的一个生成树的环边最多只有 $21$ 条，考虑将所有环边单独拿出来跑 $dijkstra$，树边使用 $LCA$ 差分进行计算。询问时，分成两类情况，只走树边和可以走环边，取最小值。

##### 例题3

给定一棵 $n$ 个点带边权的无根树，维护一个可以动态增加和删除结点的关键点集合，询问使得当前的关键点集合连通的最小子连通块的边权和。

其中 $1\le n,q \le 10^5$。

来源：[Contest Hunter Round #56 C. 异象石](https://loj.ac/problem/10132)

两个点的情况非常容易，自然想如果有 $3$ 个点怎么做，我们显然不能随便选两个点算一次距离，再将 $LCA$ 和第三个点算一次距离，因此不难发现这个距离和的计算是与遍历点集的顺序有关的。

考虑 $dfs$ 遍历一棵树的过程，我们沿着边走将树完全遍历完，每条边会在进出的时候经过两次。

利用这个想法，我们将关键点的集合按照 $dfs$ 序排序，这样从小到大遍历一趟实际上就是 $dfs$ 的过程，但是这里 $dfs$ 时不能直接得到两个相邻点之间的距离，需要用 $LCA$ 优化。因此，对于关键点集合排序后，我们只要计算所有相邻点的距离和（注意第一个和最后一个之间的距离也要计算），这个便是答案的两倍。

最后，考虑动态加点和删点，用一个 $set$ 进行维护，因此每次插入和删除时，至多只会影响左右两个点的情况。

#### 树上差分

$LCA$ 可以用于树上打标记差分。

我们知道树状数组是维护一个序列的前缀和，支持单点更新和单点查询，但是通过差分，我们可以使树状数组支持区间更新和单点查询（在之前的题目已经出现过）。

使用树状数组维护序列 $a_i$，树状数组内存的每个位置 $i$ 的定义变为 $a_i-a_{i-1}$ ，即当前位置和前一个位置的差值。考虑区间更新时，连续区间 $(l,r]$ 内的差值并没有变化，变化的只有 $l$ 位置和 $r+1$ 位置，在 $l$ 上加 $x$，在 $r+1$ 上相应 $-x$ ，即代表 $[l,r]$ 区间内全体都加上了 $x$。查询时，直接查询前缀和即可。

在树上也可以做同样的事情去更新一条路径的权值，不过要注意一点，标记的定义是一个点和其儿子之间的差值，标记是从叶子结点往根结点上传。

##### 例题1

给定一棵 $n$ 个点 $m$ 条边的无向联通图，有 $n-1$ 条边是树边，剩余边是环边。你现在需要斩断一条树边和一条环边，使得这张图不连通。求有多少种切割方案。

其中 $1 \le n \le 10^5,1\le m \le 2 \cdot 10^5$。

来源：[POJ 3417 Network](http://poj.org/problem?id=3417)

有三种情况

1. 一条树边不在任何一个环内，切断这条边，图已经不连通，再随意切一条环边即可。
2. 一条树边仅在一个环内，切断这条边和对应的环边，图不连通。
3. 一条树边在多个环内，那么必须把这些环边全部切断才能使得图不连通。

因此，问题变成如果统计一条边在多少个环内。注意到，新增一条 $u$ 连到 $v$ 的环边时，$u$ 到 $v$ 的路径上所有点都会出现在一个环内，只需要给这条路径上所有点的次数标记 $+1$ 即可。

但是我们并不需要真的去做这件事，只需要在 $u$ 和 $v$ 上面打 $+1$ 标记，在 $LCA(u,v)$ 上打 $-2$ 标记。最后，只需要遍历一遍这棵树，将标记从儿子结点不断上传合并即可。

##### 例题2

给定一棵 $n$ 个点带边权的有根树，给定 $m$ 条树上路径，要求将一条树边的权值置 $0$ 后，最小化所有路径长度的最大值。

其中 $1\le n, m \le 3 \cdot 10^5$。

来源：[NOIP2015 D2T3 运输计划](https://www.luogu.org/problemnew/show/P2680)

二分答案，$check$ 能否构造出使得最大值为 $x$ 的方案。

考虑将所有路径长度大于 $x$ 的路径端点打上标记，然后 $dfs$ 一遍上传标记，判断有没有一条边在所有长度大于 $x$ 的路径上，并且将其权值置 $0$ 后，可以使得这些路径权值都不大于于 $x$。

#### 例题

给定一棵 $n$ 个点无根树，有 $q$ 次询问，将无根树的根定为 $r$，将给出的 $k$ 个关键点划分为最多 $m$ 块，要求相同块内的点在这颗有根树上两两没有祖孙关系，求分块的方案数。

其中 $1\le n,q\le 10^5,1\le k,r\le n,1\le m \le min(300,k)$ 且 $\sum k \le 10^5$。

来源：[CodeCraft-19 and Codeforces Round #537 E. Tree](https://codeforces.com/contest/1111/problem/E)

（怎么看这个题都非常的神奇，又是换根又是要弄一棵虚树出来计数。

复习一下第二类斯特林数 $S(n,m)$，组合意义下，它表示 $n$ 个不同的物品放入 $m$ 个相同的盒子内（每个盒子非空）的方案数，第二类斯特林数有递推方程
$$
S(n,m)=S(n-1,m-1)+m\cdot S(n-1,m)
$$
上递推方程的组合意义，$n$ 个物品放入 $m$ 个盒子，是从 $n-1$ 个物品放入 $m-1$ 个盒子，新物品单独放在一个盒子内，以及 $n-1$ 个物品放入 $m$ 个盒子，新物品放入 $m$ 个盒子中的某一个，这两种情况转移而来。

于是，原题的关键点划分如果不考虑限制条件就是第二类斯特林数，但是它现在有限制条件。先确定一个根，我们可以注意到前一半的转移是没有问题的，关键是后一半的转移原来 $m$ 个盒子并非都能放入新的点。考虑 $dp$ 转移的过程，每个点不能放入的块数只和它的祖先个数有关，每个点的转移只和深度比它浅的点有关（祖先结点），我们可以对关键点按照深度排序，对于一个点 $i$，他只能够放入 $m-h[i]$ 个块内，$h[i]$ 表示 $i$ 有多少个祖先在关键点的集合内（显然这些祖先必定属于 $h[i]$ 个不同集合），有转移方程
$$
dp[i][j]=dp[i-1][j-1]+(j-h[i])dp[i-1][j](h[i]<j\le m)
$$
对于这个转移方程，可以发现有用的部分只有每个点祖先在关键点集合内的个数，和其本身无关，为了保证 $dp$ 的正确，只需要对 $h$ 排序即可。

那么现在的问题转化为如何获得关键点的 $h$，实际上考虑到换根操作，这就是等于求每个点到 $r$ 的路径上有多少个关键点。维护一下每个点到根的路径上出现了多少个关键点，更新时就是子树内所有点到根的路径上多了一个关键点，区间更新，树状数组维护。最后，单点查询用 $LCA$ 差分即可（注意差分的边界情况）。

### 树链剖分

树链剖分，Heavy path decomposition，也叫轻重链剖分，简称树剖。

#### 引入

树链剖分也是对朴素算法的一种优化，它本质上说也是优化了求 $LCA$ 时向上跳转的这一个过程。他比较优秀的地方是求 $LCA$ 的过程中，借助于线段树，实现了

1. 修改 **单个结点 / 树上两点之间的路径 / 一个结点的子树上** 的所有点的值。
2. 查询 **单个结点 / 树上两点之间的路径 / 一个结点的子树上** 结点的值的 **和(其他支持线段树区间合并的信息)**。

其中，点数 $2 \le n \le 10^5$，询问次数 $1 \le q \le 10^5$。

#### 路径剖分

对于这个问题前半部分，单点和子树的查询修改我们可以使用 $dfs$ 序来做。

对于路径信息的维护，我们显然不可能每次暴力路径进行更新。但是，类似于之前 $dfs$ 序的思路，我们需要想办法将树上的路径，转化为一些连续的区间，然后我们可以使用线段树进行维护。

![一棵树按照 $dfs$ 序进行标号](https://github.com/yjl9903/Pictures/raw/master/Tree/hld2.png)

上图将一棵树按照 $dfs$ 序进行了标号，并且若结点标号连续且在同一条链上，染成了同一种颜色。

我们可以发现，$dfs$ 序本身就将这棵树剖分成了一条条链（具体为叶子个数条树链），链上的标号是连续的，这在 $dfs$ 的过程中也很容易发现。

因此，我们可以在 $dfs$ 过程中维护一个 `top` 数组，表示当前点所处的链的链头是哪个点（即一条链上深度最浅的那个点）。

$dfs$ 过程中，对于第一个儿子，我们将他放入父亲所处的这条链内，具体来说就是设置他的 `top` 值和父亲相同即可。然后，对于其他的儿子，我们设置他的 `top` 值为其自身，表示从这个点开始新开了一条链。

算法实现参考：

```c++
void dfs(int u, int tp) {
  // 参数 tp：当前点 u 所处的链的链头
  top[u] = tp; 
  if ((int)edge[u].size() == 0) continue;
  dfs(edge[u][0], tp);
  for (int i = 1; i < (int)edge[u].size(); i++) {
    dfs(edge[u][i], edge[u][i]);
  }
}
```

除此以外，为了求 $LCA$ 时进行跳转，还需要预处理出每个结点 $u$ 的深度 `dep[u]`，每个结点的父亲`fa[u]`，每个结点的 $dfs$ 序时间戳 `dfn[u]`。

利用这些信息，我们就可以处理对链的询问或修改操作。类似于倍增的过程，我们肯定是先选一个深度比较深点往上跳转，往上跳转的过程可以利用我们已经预处理好的 `top` 和 `fa` 的信息进行加速。因为，一条链上的点已经记录他的链头 `top`，所以链上的点都可以 $O(1)$ 的时间内往上跳转。并且，这条链上的点的 $dfs$ 序时间戳是连续的，于是我们可以用线段树进行区间信息的维护。

不同于倍增的是，我们并不能往上跳转指定的步数。但是，我们会发现，每次跳转肯定是要用链头 `top` 深度较深的点进行，如果用深度浅的那个，可能往上跳转的过程会跳过最近公共祖先。当两个点跳到处于同一条链中，那么 $LCA$ 就是深度较浅的那个。

算法实现参考：

```c++
void solvePath(int u, int v) {
  while (top[u] != top[v]) { // 不在同一条链中
    if (dep[top[u]] < dep[top[v]]) swap(u, v); // 将 u 设置为深度较深的那个
    // 线段树操作
    u = fa[top[u]];
  }
  // u, v 在同一条链中
  // LCA 为 u, v 中深度浅的那个
  // 线段树操作
}
```

#### 轻重链剖分

于是，我们得到了一个将树剖分出来的算法进行路径信息维护的算法。

但是，我们现在的算法复杂度真的对嘛？

考虑这样一种蛋疼的情况：

![](https://github.com/yjl9903/Pictures/raw/master/Tree/hld3.png)

其中，右下角向下不断延伸 $n \over 2$ 层。那么最坏情况下就是询问最下面那个点和最上面那个点，这样需要向上跳转 $n \over 2$ 次，每次跳转线段树操作的复杂度是 $O(\log n)$，于是最坏情况下单次操作的复杂度是 $O(n \log n)$。

于是，和朴素算法一样，瓶颈在于往上跳转的次数太多。为了避免类似这种拖出一个长链的情况，我们来考虑怎么优化这个过程。

再来一次倍增，那怎么维护区间信息？剖分时随机选点？

轻重链剖分！

从上面那个例子我们可以想到，如果我们一开始剖分树时，都选择子树结点个数最多的那个儿子放进同一条链内，事情会怎么样？

##### 定义

记 `size[u]` 表示以 $u$ 为根的子树大小，即子树内结点个数。

记 `son[u]` 表示 $u$ 的所有儿子中，对应子树结点数最多的那个儿子，即重儿子，其余儿子均为轻儿子。

定义重边为父结点连向重儿子的那条边，轻边为除了重边外的所有边，重链为重边组成的一条树链。

##### 性质

1. 假设在以 $u$ 为根的子树中，$v$ 是 $u$ 的轻儿子，有 $size[v] \le {size[u] \over 2}$。

   证明：如果 $size[v] > {size[u] \over 2}$，那么 $v$ 的大小将大于 $u$ 的其它所有子树大小之和，这与 $v$ 是 $u$ 的轻儿子矛盾。

2. $u$ 到根路径上最多有 $O(\log n) $ 条重链或轻边。

   证明：从根结点到一个结点的路径上，每次经过一条轻边，子树大小将会至少缩小一半，因此这条路径上至多有 $\log n$ 条轻边。再考虑重链，因为重链一定是在路径的开始，结束位置或者两条轻边之间，所以重链的数量级是和轻边相同，因此 $u$ 到根的路径上最多只有 $O(\log n)$ 条重链或轻边。

![轻重链剖分后的树](https://raw.githubusercontent.com/yjl9903/Pictures/master/Tree/hld4.png)

至此，我们可以发现如果按照轻重链对树进行剖分，那么对于任意一个点，它向上跳转的次数是 $O(\log n)$ 的，如果算上线段树的复杂度，最后我们得到了一个 $O(n+qn \log^2n)$ 的优秀算法。

另外，如果我们只是需要求 $LCA$，那么预处理的时间复杂度为 $O(n)$， 单次询问的时间复杂度是 $O(\log n)$。

#### 参考代码

```c++
namespace hld {		
    // 线段树代码省略
		int siz[maxn], dep[maxn], fa[maxn], son[maxn], top[maxn], id[maxn], cnt = 0;
    void dfs(int p, int d, int old){
        dep[p] = d; fa[p] = old; siz[p] = 1;
        int m = -1;
        for (int i = head[p]; i; i = nxt[i]){
            int v = to[i];
            if (v == fa[p]) continue;
            dfs(v, d + 1, p);
            siz[p] += siz[v];
            if (siz[v] > m) son[p] = v, m = siz[v];
        }
    }
    void dfs(int p, int tp){
        id[p] = ++cnt; top[p] = tp; wt[cnt] = a[p];
        if (!son[p]) return;
        dfs(son[p], tp);
        for (int i = head[p]; i; i = nxt[i]){
            int v = to[i];
            if (v == fa[p] || v == son[p]) continue;
            dfs(v, v);
        }
    }
    void init(){ // 树链剖分初始化
        cnt = 0; dfs(1, 1, 0); dfs(1, 1); build(1, n, 1);
    }

    int qpath(int x, int y){
        int ans = 0;
        while (top[x] != top[y]){
            if (dep[top[x]] < dep[top[y]]) swap(x, y);
            ans = (ans + query(id[top[x]], id[x], 1, n, 1)) % p;
            x = fa[top[x]];
        }
        if (dep[x] > dep[y]) swap(x, y);
        ans = (ans + query(id[x], id[y], 1, n, 1)) % p;
        return ans;
    }
    int qson(int x){
        return query(id[x], id[x] + siz[x] - 1, 1, n, 1);
    }
    void upath(int x, int y, int k){
        k %= p;
        while (top[x] != top[y]){
            if (dep[top[x]] < dep[top[y]]) swap(x, y);
            update(id[top[x]], id[x], k, 1, n, 1);
            x = fa[top[x]];
        }
        if (dep[x] > dep[y]) swap(x, y);
        update(id[x], id[y], k, 1, n, 1);
    }
    void uson(int x, int k){
        update(id[x], id[x] + siz[x] - 1, k % p, 1, n, 1);
    }
}
```

#### 总结

树链剖分中轻重链剖分的思路也是后面树分治和树上启发式合并的复杂度正确性的保证。

除了轻重链的剖分方式外，还有长链剖分，虚实链剖分等剖分树链的方法。

长链剖分，定义重儿子为子树高度最高的儿子，它可以解决一类有关子树深度询问的问题，例如 $O(n \log n)$ 的时间预处理，$O(1)$ 时间内询问任意一个点的 $k$ 级祖先，以及快速合并子树内关于深度的信息。

> 具体请参考我的博客：[长链剖分](https://xlor.cn/2019/6/lpd/)
>
> 例题和题解：[HDu6634 Salty Fish 题解](https://xlor.cn/2019/8/hdu6634/)

虚实链剖分，Link-Cut Tree， 定义了 Preferred Child​ ，主要解决了动态树（树的形态不固定，允许动态加边和删边）上连通性，链的信息的维护问题。使用 Link-Cut Tree 也可以用来求 $LCA$ （误），`access(u)` 后，再 `access(v)` 时，最后一次 `splay(x)` 操作将 $x$ 转到 $splay$ 的根时，这个点 $x$ 即为 $u$ 和 $v$ 的 $LCA$。

> 动态树问题：
>
> 1. 加边和删边
> 2. 树上路径操作
> 3. 子树操作
>
> 维护操作 23 是树链剖分，维护操作 12 是 Link Cut Tree，维护操作 13 是 Euler Tour Tree，三种全部维护需要 Self-Adjusting Top Tree。（后面三个其实都没啥用，想不开的可以读论文）

其他应用，结合线段树维护 $dp$ 转移矩阵的 Trick，可以结合树链剖分的轻重链，实现在线的点权带修，询问树上最大权独立集等问题。

> 例题和题解参考我的博客：[基于变换合并的树上动态 DP](https://xlor.cn/2019/6/ddp/)

#### 例题1

给定一棵 $n$ 个点的有根树，每个点一开始的颜色 $c_i$ 为 $0$，现在有 $m$ 次操作，每次将 $u$ 到 $v$ 的路径上染上了颜色 $c$。求每个点第 $k$ 次染的颜色是什么，若不足 $k$ 次，则输出 $0$。

其中 $1 \le n,m,k \le 10^5, 1\le c_i \le 10^9$。

来源：[牛客国庆集训派对 Day 6 I. 清明梦超能力者黄YY](https://ac.nowcoder.com/acm/contest/206/I)

修改操作是区间覆盖，肯定可以用线段树来做。因为要求第 $k$ 次时的颜色，因此第 $k$ 次前后的染色具体是什么就没有什么用，所以我们对我们每个区间维护一下这个区间染色次数的最小值和最大值。线段树更新操作时，如果当前区间最少已经更新了 $k$ 次，那么直接返回不进行更新。如果当前区间最大更新了不超过 $k-1$ 次，那么直接区间加一即可。当且仅当，一个单点更新了 $k-1$ 次时，才会为它染上颜色。

因为，每个点最多被染色一次，所以时间复杂度为 $O(n \log^2 n)$。

#### 例题2

交互题，给定一棵 $n$ 个点的无根树，猜一个隐藏的点 $x$。

有 $36$ 次询问，询问一个点 $q$ 到 $x$ 的距离，或者询问点 $q$ 到 $x$ 的路径上下一个点。

其中 $2 \le n \le 10^5$。

来源：[Codeforces Round #563 (Div. 2) F. Ehab and the Big Finale](https://link.zhihu.com/?target=https%3A//codeforces.com/contest/1174/problem/F)

最开始时，询问到根的距离，这样就有很多备选解，考虑将这个解空间缩小。

再考虑给定的询问方式，我们于是需要加速一个点往下跳转的过程，并且保证正确性，而不是一步一步走。

考虑轻重链剖分，当前考虑的根为 $u$，重链的底端为 $v$，考虑 $v$ 和 $x$ 的最近公共祖先，联想树上差分距离这个过程的逆，可以得到这个 LCA。

如果 LCA 不是答案，那么将 LCA 往下移一步递归地解决即可。

显然，轻链的个数是 $O(\log n)$ 的，每次最多只能询问两次。

注意：有一个询问可以在缩小问题规模时维护出来，无需单独询问。

类似于轻重链剖分的做法，也可以用重心分解来加速跳转。

讨论一下 $x$ 是不是在当前的重心内，在重心内就往下走一步，否则取当前的子问题为重心的父亲，并标记删除。

### 转化为 RMQ 问题

对有根树进行遍历，每进入到一个结点加入到序列中，每从一条边遍历回来把当前结点加入到序列中，因此我们得到了一个欧拉序列。

![](https://github.com/yjl9903/Pictures/raw/master/Tree/lca.png)

欧拉序（第一行为时间戳，第二行为结点编号）：

| 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   | 12   | 13   | 14   | 15   | 16   | 17   | 18   | 19   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1    | 2    | 5    | 2    | 6    | 9    | 6    | 10   | 6    | 2    | 1    | 3    | 1    | 4    | 7    | 8    | 7    | 4    | 1    |

结点第一次进入的时间戳：

|  结点  |  1   |  2   |  3   |  4   |  5   |  6   |  7   |  8   |  9   |  10  |
| :----: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| 时间戳 |  1   |  2   |  12  |  14  |  3   |  5   |  15  |  16  |  6   |  8   |

我们可以发现任意两个结点的最近公共祖先，是这两个点第一次进入位置对应的区间深度的最小值的对应位置。

于是，我们可以对欧拉序建立 $ST$ 表，查询区间最小值的位置。

预处理时间复杂度 $O(n \log n)$，单次询问时间复杂度 $O(1)$。

> 使用标准 RMQ，可以做到 $O(n)$ 的时间内预处理，但是编程复杂度很高。
>
> 流程：
>
> 1. 通过 $dfs$ 序将树上 $LCA$ 问题转化为序列 RMQ 问题
> 2. 单调栈将序列转化为笛卡尔树
> 3. 在笛卡尔树上求欧拉序，转化为 $\pm 1$ RMQ 问题
> 4. 对新序列分块，做分块 ST 表，块内通过二进制状压 DP 维护

### Tarjan

求 $LCA$ 的 Tarjan 算法是一个离线算法，时间复杂度为 $O(n+q)$ 。

> 离线算法是将所有要求的操作记录下来，重新组织计算顺序，统一进行计算。

Tarjan 算法核心是进行一次深度优先搜索，类似于转化为 RMQ 问题的思路，$u$ 和 $v$ 的最近公共祖先就是从 $u$ 遍历到 $v$ 这个过程中，深度最小的点就是 $LCA$。于是，我们将每个询问放到对应的结点上，处理一个结点上的询问时，就是判断另外一个点是否访问过，如果另外一个点访问过的话，我们要找出它是回溯到哪个时才遍历到这个点，这个可以用并查集进行维护。

### 对比

| 算法            | 预处理复杂度  | 单次询问复杂度 |
| :-------------- | :-----------: | :------------: |
| 倍增            | $O(n \log n)$ |  $O(\log n)$   |
| 转化为 RMQ 问题 | $O(n \log n)$ |     $O(1)$     |
| 树链剖分        |    $O(n)$     |  $O(\log n)$   |

在算法竞赛中，大部分情况下，倍增和树链剖分求 $LCA$ 在时间复杂度和编程复杂度上达到了平衡，大部分需要使用 $LCA$ 的问题都可以使用这两个算法进行求解。某些时间复杂度卡的比较死的情况下，可能需要 Tarjan 的离线算法或转化为 RMQ 问题。

## 树分治

> 具体请参考 [CCPC Wannafly Winter Camp Div1 Day4 数据结构（吴作凡）](https://www.zhixincode.com/live/10615/replay?replayId=13904)

我们在处理序列上的问题时，经常会使用一些分治的算法，也就是将一个大的问题分成多个规模更小的子问题分别处理，然后可能会再将这些子问题的信息进行一些合并或更新操作，以达到高效解决问题的效果。例如，排序中的归并排序算法，算法竞赛中常见的线段树等等都是分治的结构。

于是，我们就想能不能把分治的结构也放到树上，去解决一些问题。考虑有两种分割的方式，将一条边切开分成两部分处理，即边分治，将连接一个点的所有边都切开，递归处理每一棵子树，即点分治。

后面主要介绍点分治。首先考虑一个问题，我们选择一个点把这个点所有子树全部切开来处理，我们应该如何选择这个点？显然不能随意选择一个点，对于一条链的情况，递归分治的层数会到达 $O(n)$。

类似于序列上的分治方法，序列上我们能够恰好将问题一分为二，我们希望在树上也能够尽量一分为二，使得递归层数尽可能少。因此，我们要找一个特殊的点，重心，这棵树上以这个点为根时，最大的儿子最小的那个点。

### 重心

重心的性质，以这个点为根，所有子树的大小都不超过整个树大小的一半。这样做的好处是分治结构，每层问题规模都会至少缩小一半，于是递归的层数 $O(\log n)$ 的。

证明一下上面这个性质：

反证法，假设重心 $u$ 上存在一棵以 $v$ 为根的子树的大小大于 $n\over 2$，那么对于 $v$，$u$ 和其他所有子树是 $v$ 的子树，而这棵子树的大小是小于 $n\over2$ 的，$v$ 的所有子树大小肯定小于 $size(v)$，因此我们找到了一个更最大儿子更小的点 $v$，故矛盾。

> 从上面的证明中，我们可以看出要么只有一个重心，要么有两个重心，且他们的子树大小都是 $n \over 2$。

找重心的参考代码：

```c++
// size[u] 为 u 的子树大小
// root 记录当前最大儿子最小的那个点，mn 为其大小
void getrt(int u, int f) {
  size[u] = 1;
  int mx = 0;
  for (int& v: edge[u]) {
    if (v == f) continue;
    getrt(v, u);
    size[u] += size[v];
    mx = max(mx, size[v]);
  }
  mx = max(mx, n - size[u]);// 子树外的大小
  if (mx < mn) {
    mn = mx; root = u;
  }
}
```

### Free Tour II

点分治的重要应用是可以统计树上路径信息的一类问题。

给定一棵 $n$ 个点的带边权无根树，每个点有黑白两种颜色，求树上的一条最长路径使得这条路径上最多有 $k$ 个黑点。

其中 $2 \le k \le n \le 2 \times 10^5$。

来源：[SPOJ Free Tour II](https://www.spoj.com/problems/FTOUR2/en/)

考虑点分治，每次处理所有过根结点的路径。

假设我们已经处理完了 $u$ 的前 $m$ 个子树，我们需要用第 $m+1$ 棵子树的信息更新答案，并合并这两个集合的信息。

对于第 $m+1$ 棵子树，我们需要子树内所有点到根路径上的黑点数 $cnt$ 和路径长度 $dis$，询问时只需要在前 $m$ 棵子树的信息内查询黑点数小于 $k-cnt+black[u]$ 的最大路径长度即可，这个可以使用线段树（树状数组）维护。

时间复杂度：$O(n \log^2 n)$。

使用启发式合并可以做到 $O(n \log n)$。

## 树上启发式合并

> Codeforces 原文链接：[dsu on tree](https://codeforces.com/blog/entry/44351)

在并查集中，有两个优化，大部分情况下都是写路径压缩优化就足以达到复杂度的要求，但是在某些无法压缩路径的问题中，我们需要使用启发式合并。他的主要思路是合并集合时，将小的集合合并到大的集合中，以减小树的高度。

树上启发式合并，推广者称其为 DSU on tree，就是将并查集的启发式合并思路放到树上，能够解决一些静态树上的子树信息查询问题。

### 例题

$n$ 个选手，一开始每个人被随机发了一张石头剪刀布卡片，显然有 $3^n$ 种分配方式。

一开始，第 $i$ 个选手坐在第 $i$ 个位置上，有 $m$ 个操作，操作有两种：

1. `1 x y`：撤去座位 $y$ ，$y$ 上的人 $b$ 和坐在 $x$ 上的人 $a$ 石头剪刀布，如果 $b$ 赢了则 $a$ 被淘汰，$b$ 坐在 $x$ 上，否则 $b$  被淘汰（保证数据合法）。
2. `2 x`：进行了之前的一类事件后，有多少种分配方式使得第 $x$ 个选手没被淘汰。

其中 $1\le n,m\le 2 \cdot 10^5$。 

来源：[CCPC-Wannafly Winter Camp Day3 (Div1, onsite) I. 石头剪刀布](https://www.zhixincode.com/contest/12/problem/I?problem_id=191)

首先，我们从概率的角度理解问题，而不是计数。

初始状态下，任何一个人的存活概率为 $1$。

对于每次比赛，连一条从客场作战者到主场作战者的边。

进行一次比赛后，主场作战者的存活概率为 $2\over3$，客场作战者的存活概率为 $1 \over 3$。而主场作战者可能不是原位置上的人，而是这个位置所属于的块内的某一个人，这个块内的每个人存活概率都变成了原来的 $2 \over 3$，客场作战者同理。

因此，我们需要维护的是一个联通块，联通块的合并以及每个点到这个块内根结点的权值乘积，带权并查集启发式合并即可。

### 树上众数

给定一棵 $n$ 个点的有根树，每个结点有一个颜色 $c_i$，询问每个点的子树内出现次数最多的颜色（如果有多个颜色出现次数最多，输出这些颜色的编号和）。

其中 $1 \le n,c_i \le 10^6$。

来源：[Educational Codeforces Round 2 E. Lomsat gelral](https://codeforces.com/problemset/problem/600/E)

> 乍看一下要用树套树等高级数据结构来实现？

假如数据范围变成 $1 \le n \le 1000$，这个题怎么做？

直接暴力 $O(n^2)$ ！

我们遍历每个点，然后暴力这个点的子树，维护所求的答案。

#### 参考代码

```c++
int color[maxn], bag[maxn], result[maxn], mx = 0, answer = 0;
void add(int u, int f) {
  bag[color[u]]++;
  if (bag[color[u]] == mx) {
    answer += color[u];
  } else if (bag[color[u]] > mx) {
    answer = color[u];
    mx = bag[color[u]];
  }
  for (int& v: edge[u]) {
    if (v == f) continue;
    add(v, u);
  }
}
void dfs(int u, int f) {
  add(u, f);
  result[u] = answer;
  mx = 0; 
  answer = 0;
  memset(bag, 0, sizeof bag);
  for (int& v: edge[u]) {
    if (v == f) continue;
    dfs(v, u);
  }
}
```

#### 启发式合并

我们现在的算法是每次遍历一个点计算答案，然后清空这棵子树的信息。显然，我们希望能够从子树保留一些信息回来，但是我们又不得不去清空某些子树信息信息，否则会影响不相干的其他子树的答案。进一步，我们肯定希望能够保留信息足够多，这样才能优化时间。

因此，轻重链剖分！

对于一个点 $u$ 的所有儿子，我们先 $dfs$ 遍历所有轻儿子，解决完轻儿子对应的子树后，清空这个轻儿子的信息。最后，我们 $dfs$ 遍历重儿子，解决完重儿子的子树后，不清空这个重儿子的信息，而是保留下来，暴力轻儿子将信息合并进去。

这个过程就类似于轻重链剖分，显然每个叶子都会属于一条重链，重链上从叶子到链头的每个结点，依次将信息上传上去。而对于一条轻边，会清空当前全局数组内保存的所有信息。

根据树链剖分中的结论，任意一个点到根的路径上最多有 $\log n$ 条轻边。在这里，对于结点 $u$ 的所有祖先，如果是一个轻儿子，那么 $u$ 就会被暴力更新一次。因此，总体上时间复杂度为 $O(n \log n)$ 的（假设更新操作的复杂度是 $O(1) $）。

这个过程，其实就是小的集合合并进大集合内，即启发式合并。

##### 参考代码

```c++
int n, c[maxn];
ll ans[maxn], cnt[maxn];

int sz[maxn], son[maxn];
// 预处理出重儿子
void getsz(int u, int f){ 
    sz[u] = 1; 
    for (auto& v : edge[u]){
        if (v == f) continue;
        getsz(v, u); sz[u] += sz[v];
        if (sz[v] > sz[son[u]]){
            son[u] = v;
        }
    }
}
ll res = 0; 
int mx = 0; 
bool sk[maxn];
// 更新答案
void add(int u, int f, int x){
    cnt[c[u]] += x;
    if (x > 0 && cnt[c[u]] >= mx){
        if (cnt[c[u]] > mx) res = 0, mx = cnt[c[u]];
        res += c[u];
    }
    for (auto& v : edge[u]){
        if (v == f || sk[v]) continue;
        add(v, u, x);
    }
}
void dfs(int u, int f, int kp) { // kp 标记该子树信息是否应该被清空
    for (auto& v : edge[u]){
        if (v == f || v == son[u]) continue;
        dfs(v, u, 0);
    }
    if (son[u]) dfs(son[u], u, 1), sk[son[u]] = 1;
    add(u, f, 1);
    ans[u] = res;
    if (son[u]) sk[son[u]] = 0;
    if (!kp) add(u, f, -1), res = mx = 0; // 直接 memset 就超时了
}
```

### 例题1

给定一个 $n$ 个点 $m$ 条边的任意无向图，每个结点本身有一个颜色 $a_i$，现在要给所有结点分别染色，要求满足一个联通块内的结点必须染上相同的颜色。分别询问每条边，删除这条边后，最多有多少结点染的颜色和其本身颜色相同。

其中 $1 \le n,m,a_i \le 10^5$。

来源：[2018 ACM-ICPC Asia Qingdao Regional Contest B. Kawa Exam](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemCode=4059)

在一个联通块内，如果这条边在一个环内，删除这条边后，原联通块仍然联通，对于答案无影响。只有对于桥边，删除后会变成两个联通块，才会对答案有影响。

因此，首先需要用边双联通分量缩点，形成一个森林。

对于，森林内的每棵树，我们需要计算删除每一条树边后，子树内和子树外出现次数最多的次数，加上森林内其他树的答案即可。

对于子树内的众数，这是树上启发式合并，同样对于子树外的众数，反过来维护即可。

修改和查询最大值时，我们需要一个复杂度都是 $O(1)$ 的神奇数据结构。因为修改都是 $+1$ 或 $-1$，因此维护每种出现次数的个数，就可以实现这个神奇的数据结构。

注意：树上启发式合并时，缩点大小最好设置为对应块的大小。

### 例题2

给定一棵 $n$ 个结点的有根树，每条边有一个 $a$ 到 $v$ 的字母作为权值，询问每个点的子树内最长的简单路径，满足路径上边的字母可以打乱顺序拼成回文串。

其中 $1 \le n \le 5\cdot 10^5$。

来源：[Codeforces Round #383 (Div. 1) D. Arpa’s letter-marked tree and Mehrdad’s Dokhtar-kosh paths](https://codeforces.com/contest/741/problem/D)

一串字母能否变成回文串的条件是最多只能有一个字母出现了奇数次，因此对于一个路径，我们只需要记录上面每个字母出现次数的奇偶性即可。

因为最多只有 $22$ 种字母，因此我们可以把这个状态压成 $bitmasks$，开一个数组记录。

考虑树上启发式合并，对于每一棵子树 $u$，我们需要遍历从 $u$ 开始的每条路径，并维护每种串的最大深度。

为了避免两种状态来源于同一个儿子，因此每次暴力子树信息时，先对这个儿子内每种状态询问 $23$ 次（出现次数全部为偶数和 $22$ 种一个字母出现了奇数次）对应合法合并状态的最大深度，然后用 $LCA$ 进行差分得到路径长度，更新答案，之后再当前儿子与之前的信息合并起来。

## 树哈希

$$
f_{now}=1+\sum f_{son_{now,i}} \times prime(size_{son_{now,i}})
$$

其中 $f_x$ 为以结点 $x$ 为根的子树对应的哈希值。

$size_{x}$ 表示以结点 $x$ 为根的子树大小。

$son_{x,i}$ 表示 $x$ 所有子结点之一（不用排序）。

$prime(i)$ 表示第 $i$ 个质数。

> 参考博客 [树hash](https://www.cnblogs.com/huyufeifei/p/10817673.html)
>
> 其他哈希方式的一些 Hack [树哈希 - OI wiki](https://oi-wiki.org/graph/tree-hash/#problem-hdu-6647)

### 例题

求遍历一棵无根树产生的本质不同括号序列方案数。

其中 $2 \le n \le 10^5$。

来源：[HDu6647 Bracket Sequences on Tree](http://acm.hdu.edu.cn/showproblem.php?pid=6647)

首先，注意到一个结论，对于两棵有根树，如果他们不同构，一定不会生成相同的括号序列。我们先考虑遍历有根树能够产生的本质不同括号序列方案数，假设我们当前考虑的子树根结点为 $u$，记 $f(u)$ 表示这棵子树的方案数，$son(u)$ 表示 $u$ 的儿子结点集合，从 $u$ 开始往下遍历，顺序可以随意选择，产生 $|son(u)|!$ 种排列，遍历每个儿子结点 $v$，$v$ 的子树内有 $f(v)$ 种方案，因此有 $f(u)=|son(u)|! \cdot \prod_{v\in son(u)} f(v)$。但是，同构的子树之间会产生重复，$f(u)$ 需要除掉每种本质不同子树出现次数阶乘的乘积，类似于多重集合的排列。

通过上述树形 dp，可以求出根结点的方案数，再通过 up and down 树形 dp，将父亲结点的哈希值和方案信息转移给儿子，可以求出以每个结点为根时的哈希值和方案数，每种不同的子树只需要计数一次即可。

注意，本题数据较强，树哈希很容易发生冲突。这里提供一个比较简单的解决方法，求出一个结点子树的哈希值后，可以将其前后分别插入一个值再计算一遍哈希值。

## 问题

### 例题1

给定一棵 $n$ 个点的无根树，树上有 $k$ 个关键点，你现在需要派人选择占领一些关键点，占领一个关键点后可以管理与之联通的一定个数的结点，现在要求对于所有管理方案，管理最多结点的人，管理的结点个数最少是多少。

其中 $1\le n,k\le 2\cdot 10^5$。

来源：[EOJ Monthly 2018.12 E. 管理孩子](https://acm.ecnu.edu.cn/problem/3654/)

二分答案，$check$ 是否能构造出管理的最多结点个数为 $mid$ 。

对于每个子树，维护一个权值：这棵子树中的某个结点最多还能够覆盖多少结点（+）或者这棵子树还有多少个结点没有被覆盖到（-），可以用正负号表示这两种权值（不会同时出现）。

$dfs$ 到某一个结点时，考虑这个结点所有儿子的权值，这些权值有正有负，所有负的权值的和，也就是这个子树总共还有多个点没有被覆盖。

显然最多只有一个带有正权值的儿子对这个子树的覆盖有贡献，因为如果覆盖到了根结点，那么其他正儿子就不能再覆盖根结点了（不联通），所以需要选择一个权值最大的正儿子。

如果正儿子权值比较大，就让这个顶点覆盖这个子树所有未覆盖顶点，然后将还能够覆盖的个数回溯到当前子树的父亲。

如果正儿子权值比较小，那么就需要当前子树的父亲来覆盖自己，将还需覆盖的个数回溯回去。

如果构造成功，那么根结点会返回一个非负值。否则，表示根结点的父亲需要提供一些覆盖给这棵树，即构造失败。

### 例题2

给定一棵 $n$ 个点带边权的无根树，要求在在树上选一些边，构成树上的正好 $m$ 条路径，并且每条边最多只能在一条路径中出现，要求最大化每个选择方案中的最小路径长度。

其中 $1 \le n \le 5\cdot 10^4, 1 \le m \le n-1$。

来源：[NOIP2018 D1T3 赛道修建](https://www.luogu.org/problemnew/show/P5021)

二分答案，$check$ 能否构造出 $m$ 条路径，使得最小路径长度为 $mid $。

  假设树是一条链，二分答案，$check(mid)$ 判断能不能将一条链划分成 $m$ 个最小值为 $mid$ 的段，每段可以贪心的选取大于等于 $mid$ 的最小段。

假设树是一棵菊花树，只有两种情况，一种是只选择一条边，第二种是选择跨过根节点的两条边。长度大于等于 $mid$ 的链，单独构成一条链，对于长度小于 $mid$ 的边，贪心地去和最小的能连在一起长度大于等于 $mid$ 的边组成一条链。

对于一般树，树上每个节点与其父亲只有一条边，因此在一个节点完成类似于菊花情况的匹配的时候，可能会剩下多条边，而这些边中选一个最大的传递到父节点，与当前节点到父节点的边组成一条链，在父亲节点类似的做菊花情况的操作。

### 例题3

角谷猜想。除 $1$ 外所有数字 $x$，奇数和 $3x+1$ 连边，偶数和 $x \over 2$ 连边。你需要从 $1,2,\dots,n$ 中选出一些数字，选数字 $i$ 获得价值 $f[i]$ ，选了数字 $i$ 和 $j$ 之间有连边，失去价值 $d[min(i,j)]$，要求最大化价值。

其中 $1 \le n \le 1000$。

来源：[CCPC-Wannafly Winter Camp Day1 (Div. 2) E. 流流流动](https://www.zhixincode.com/contest/7/problem/E?problem_id=106)

注意到如果 $1$ 没有和 $4$ 连边的话，这个图是没有环的，因为如果有环，那么存在环上的数字 $x$ 不会通过变换回到 $1$，与角谷猜想矛盾（在所给数据范围内，角谷猜想被验证正确）。

因此这个图实际上是一个森林，简单树形 $dp$ 即可。

### 例题4

一开始给定一棵 $n$ 个点的无根树，有 $m$ 次操作，每次将在树上选一个根，然后将上一次操作得到的树接到这棵树上的每一个顶点上，得到一颗新树，求这 $m+1$ 棵树的每个的最大独立集大小。

其中 $1 \le n, m\le 10^5$。

来源：[CCPC-Wannafly Winter Camp Day3 (Div. 2) E. 最大独立集](https://www.zhixincode.com/contest/15/problem/E?problem_id=217)

第一次最大独立集可以用树形 $dp$ 容易求解。

之后每次生成新树的时候，应该将每个前一棵树的最大独立集都选出来，这样显然是最优的。也就是当前树上的节点都不能选取，然后选取了 $n$ 次前一棵树的最大独立集。

然后继续考虑这样做的正确性，显然如果前一棵树的根节点被选上了，那么新树的每一个点一定不能选。如果选出来了，可能导致答案不优。

我们继续考虑一种情况，如果一棵树的根节点选不选对这棵树的最大独立集大小没有影响，那么我们不选这个根节点。考虑下一棵树，因为前一棵树的根节点没有选，所以新树中本来的点还可以再可以选出一个最大独立集，这样这棵树的答案一定比选前一棵树根时的答案优。

最后，我们得到了一个贪心的做法：如果上一棵树的树根可以不选（比较选不选时的答案），那么就不选，那么当前答案为当前树的最大独立集 + $n$ 倍的前一棵树的最大独立集大小；如果上一棵树的树根必选，也就是不选时上一棵答案不优，那么当前答案为 $n$ 倍的前一棵树的最大独立集大小。

至此，我们需要一趟树形 $dp$ 得到每棵子树的最大独立集，第二趟 $dp$ 得到以每个点为根时的最大独立集大小，然后递推即可。

### 例题5

你有 $b$ 块钱去超市购物，现在超市有 $n$ 个物品，每个物品有一个价格 $c_i$ ，使用优惠券能够优惠的价格 $d_i$。但是，优惠券不能直接使用，如果你要使用优惠券购买物品 $i$，就必须先用优惠券购买物品 $p_i$，但是你可以直接用优惠券买 $1$ 号物品，并且保证依赖关系不成环。求你最多能够购买多少件物品。

其中 $1 \le n \le 5000, 1\le b \le 10^9$。

来源：[Codeforces Round #419 (Div. 1) C. Karen and Supermarket](https://codeforces.com/problemset/problem/815/C)

树形依赖背包。因为背包容量很大，问题转化为买 $x$ 件物品花的最少钱数。

记 $dp[u][m][2]$ 表示在子树 $u$ 内买 $m$ 件物品，结点 $u$ 是否使用优惠券，花的最少钱数。

初始状态：
$$
dp[u][0][0]=0 \\
dp[u][1][0]=c_i \\
dp[u][1][1]=c_i-d_i
$$
最后一个初始状态表示 $u$ 的子树，如果选了一个必定是子树的根 $u$，否则不满足依赖关系。

设 $u$ 的一个儿子为 $v$，有转移方程
$$
dp[u][i+j][0]=min(dp[u][i][0]+dp[v][j][0]) \\
dp[u][i+j][1]=min(dp[u][i][1]+dp[v][j][0]) \\
dp[u][i+j][1]=min(dp[u][i][1]+dp[v][j][1])
$$
第一个方程意义是如果 $u$ 没有用优惠券，那么它的所有儿子都不能用优惠券，第二三个方程意义是 $u$ 用了优惠券，那么它的儿子就无所谓用不用优惠券，选择更小的即可。

每次转移的复杂度是 $O( n^2)$，总体复杂度是 $O(n^3)$，并不能通过这个题目。

我们容易注意到一件事，如果子树大小比较小，那么转移时容量较大情况并没有什么用，不需要每次都需要 $O(n^2) $ 的转移。进一步，转移时的枚举上限实际上和子树大小相关。

记 $size(u)$ 表示子树 $u$ 的大小。

假设我们在进行子树 $u$ 中的转移，当前已经遍历过 $v_1,v_2,\dots ,v_k$，因此此时最多有物品数 $sum$

$$
sum=1+size(v_1)+size(v_2)+\dots+size(v_k)
$$

那么，遍历完 $v_{k+1}$，合并状态时，对于 $u$ 的状态只需要遍历 $1$ 到 $sum$ 个，对于 $v_{k+1} $ 的状态只需要遍历 $1$ 到 $size(v_{k+1})$。

看起来复杂度没什么变化？但是，实际上，在子树 $u$ 的每次转移过程中，我们只两两合并了子树内 $LCA $ 为 $u$ 的点对状态，而这个点对的总数 $O(n^2)$ 的。于是，时间复杂度为 $O(n^2) $。

> 树形依赖背包其它例题：
>
> [2019 年江苏省赛 I Neko and tree](https://vjudge.net/problem/HDU-6540)
>
> [2018 年宁夏邀请赛 G Factories](https://codeforces.com/gym/102222/problem/G)

### 例题6

给一棵有根树 $T=(V,E)$，$X$ 是 $V$ 的非空子集，记 $f(X)$ 表示 $T$ 中包含点集 $X$ 的最小联通块中的边数，求

$$
\sum_{X\subseteq V,X \neq \varnothing} (f(X))^k
$$

其中 $2\le|V| \le 1e5,1\le k \le 200$ 。

来源：[Codeforces Hello 2019 G. Vladislav and a Great Legend](https://codeforces.com/contest/1097/problem/G)

显然，$k=1$ 直接扫一遍算贡献即可。

考虑 $k>1$ 的情况，由第二类斯特林数展开

$$
(f(X))^k=\sum_{i=0}^{k}S(k,i)i!{f(X) \choose i}
$$

前面一部分可以预处理，也就是要求对于 $i (0\le i \le k)$

$$
\sum_{X\subseteq V,X \neq \varnothing} {f(X) \choose i}
$$

设 $E' \subseteq E$ 且 $|E'|=i$，$E_X$ 为 $f(X)$ 对应联通块的边集，上式可以写成

$$
\sum_{X\subseteq V,X \neq \varnothing} \sum_{e_j \in E',1\le j \le i} [e_1,e_2,\dots,e_i \in E_X]
$$

于是，交换求和顺序

$$
\sum_{e_j \in E',1\le j \le i} \sum_{X\subseteq V,X \neq \varnothing} [e_1,e_2,\dots,e_i \in E_X]
$$

上式含义是枚举边集 $E$ 的大小为 $i$ 的子集 $E'$，计算 $E'$ 出现在多少个点集 $V$ 的非空子集 $X$ 对应的联通块内。

考虑 $dp[i][j]$ 表示以 $i$ 为根的子树内大小为 $j$ 的边集，在子树内对上式的贡献。

设当前计算的根为 $u$，有三种情况：

1. $u$ 到子树的边单独构成边集。

2. $u$ 到子树的边与这棵子树的边集合并。

3. $u$ 的子树的边集之间合并，子树的边集包含前两种情况。

对于以 $v$ 为根的子树，更新 $v$ 的 $dp$ 状态，情况一就是 $dp[v][1]+2^{size(v)}-1$，而情况二是用 $dp[v][i]$ 更新 $dp[v][i+1]$ 。得到每一个子树 $v$ 的 $dp$ 状态，做一个卷积即可得到根 $u$ 的 $dp$ 状态。

考虑卷积的过程，如果 $v_1$ 和 $v_2$ 合并，没有考虑到 $u$ 的其它子树对其的贡献。

为了解决这个问题，卷积过程中对于 $0$ 次项，设置其为 $2^{size(v)}$，这样没有参与合并的子树贡献就被计算进去了，注意这个部分的贡献不需要 $-1$。因为只要参与了合并，那么只有端点的子树才是必须非空，路径上的点都是可取可不取。

但是，状态转移的过程中，实际上已经对最终答案产生贡献，因为 $dp[i][j]$ 考虑的点集都是以 $i$ 为 $LCA$ 的。

对答案的贡献也有类似的三种情况。

情况一和情况二只需要考虑 $u$ 外的节点是否被取，更新答案即可。

情况三需要考虑合并后的边集对答案贡献，注意到卷积后实际上有一些是没有合并的，我们需要从 $u$ 的 $dp$ 状态中减去前两种情况，这有卷积结果就只剩下参与过合并的边集了。这部分边集同样是考虑 $u$ 外的节点是否选取。

于是，我们就得到了一个 $ans$ 数组表示大小为 $i$ 的边集 $E'$ 的贡献，使用第二类斯特林数即可得到最终答案。

时间复杂度 $O(nk^2)$。

当然这是过不了的，最后参考树形依赖背包的优化原理，上面的卷积过程，实际上与子树大小有关，用类似的方法，我们最终得到时间复杂度为 $O(nk)$ 的解法。

## 其他科技 / Trick

### 点集的直径

树上的两个点集 $A,B$，点集 $A$ 的某一条直径端点 $a_1,a_2$，点集 $B$ 的某一条直径端点 $b_1,b_2$，则两个点集之间的直径一定是这 $4$ 个点中最长的距离。反证法，容易证明。

#### 例题

给定一棵 $n$ 个点的无根树，边权带修，强制在线，询问直径。

其中 $3 \le n, q \le 10^5$。

来源：[CEOI 2019 day 1 Dynamic Diameter](https://codeforces.com/contest/1192/problem/B)

树状数组和 $LCA$ 解决带修路径长度。线段树维护区间的直径和直径端点。

对于修改操作，先修改边权，然后线段树上重构可能跨过这条边的线段树区间直径。

记修改边的深度大的结点为 $u$，注意到 $u$ 的 $dfs$ 序区间内的直径一定不变，以及外部的区间一定不变，这个过程等价于在线段树上 $dfs$ 了 $u$ 的 $dfs$ 序区间，重构一下经过的所有结点。

时间复杂度 $O(n\log n + q \log ^2 n)$。

### 基环树

$n$ 个点 $n$ 条边的联通图是一棵基环树，边由 $n-1$ 条树边加上一条环边组成。

#### 思路

+ 拆出一条环边，剩余当成树做。

+ 抠出整个环，环上每个点挂了一棵子树，先做 Tree dp，在环上做序列 dp。

> 例题和题解参考我的博客：[基环树](https://xlor.cn/2019/7/ring-tree/)

### up and down 树形 dp

使用第一遍树形 $dp$ ，求出子树的信息和，第二遍树形 $dp$ 实现从父亲到儿子的换根操作，以求出无根树时，每个点作为根的信息。

> 例题：
>
> + [Codeforces AIM Tech Round 3 (Div. 1) Centroids](https://codeforces.com/contest/708/problem/C)
>
> + [2019 年杭电多校第 3 场 K 题](http://acm.hdu.edu.cn/showproblem.php?pid=6613)
> + [2019 年杭电多校第 7 场 B 题](http://acm.hdu.edu.cn/showproblem.php?pid=6647)
> + [2019 年杭电多校第 8 场 F 题](http://acm.hdu.edu.cn/showproblem.php?pid=6662)

## 最后

“树论” 介绍了在一般树上的算法和问题。

$dfs$ 序的性质和应用，使用 $dfs$ 序将树上问题放到序列上处理，利用 $dfs$ 遍历树过程中的特性处理问题。介绍了计算最近公共祖先的朴素算法，倍增算法，转化为 RMQ 问题和离线的 Tarjan 算法，以及处理树上路径操作问题的轻重链链剖分算法，并应用最近公共祖先和差分的思想，高效处理了几个树上路径长度和打标记的问题。最后，树分治结构可以解决一类在无根树上的路径统计问题，树上启发式合并可以解决一类在静态树上子树信息查询问题。

报告主线是围绕树上的算法，暗线是无处不在的二进制思想。使用基于分治结构的线段树，二分等处理 $dfs$ 序上的问题。计算最近公共祖先时，倍增预处理 $u$ 的 $2^i$ 级祖先，加速向上跳转 $k$ 级祖先的过程。树链剖分，由于轻儿子的子树大小规模总是减少一半，得到 $O(\log n)$ 的跳转次数。树分治中，由于重心的性质，使得树分治结构能够如同序列分治一样，每次都将问题规模缩小一半。

例题选择自一些笔者觉得比较有趣的问题进行分享，希望树上的这些算法和问题对大家有所帮助w。
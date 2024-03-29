---
mathjax: true
title: 2017 年北航暑假集训 - 字符串
date: 2019-12-09 21:30:28
tags:
- String
categories:
- 字符串
---

| solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  |
| :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|   7    |  .  |  .  |  O  |  .  |  .  |  O  |  O  |  O  |  O  |  O  |  O  |  O  |  .  |

<!--more-->

# 题解

## A Password Suspects

UnSolved.

## B Suffixes and Palindromes

UnSolved.

## C Regular Number

给定一个长度为 $1000$ 的形如 $(0|1|2)(3|4)(5|6)\dots$ 的正则表达式，求输入串有多少个子串可以匹配。

`bitset` 暴力，时间复杂度 $O(|s|\frac{n}{w})$。

注意：

1. `bitset` 是对模式串做，即 $dp(i,j)$ 表示数字 $i$ 可以匹配第 $j$ 个串。

2. 常数优化，使用 `puts` 而不是 `putchar`。

## D To Queue or not to Queue

UnSolved.

## E Circular Palindromes

UnSolved.

## F Forensic Examination

后缀自动机 + 线段树合并模板题。

## G Palindromic Border

求输入串的每个子串的回文 $border$ 个数和。

考虑一系列本质相同的回文子串，两两拼起来可以对某些子串产生贡献。

## H Yong Zheng's Death

输入一个 Trie，求将 Trie 上每一对结点对应串拼起来，能产生多少种不同的串。

考虑计算反面，显然去重前有 Trie 树结点数的平方个串，去除一个串由多个串拼出来的方案数。

对于生成串 $s=pre+suf$，考虑将贡献记在所有拼凑方案中 $suf$ 最长的那个。枚举 $s=A+B$ 中的 $B$ 串，会发生重复，当且仅当该结点的 ac 自动机的 fail 指向了非空结点 $x$，由此产生的方案数重复就是 Trie 中后缀为 $B[1\dots |B|-|x|]$ 的所有串。

## I Square Revolution

求输入串中有多少个子串不含平方串前缀或后缀。

结论：一个串的本源平方串最多只有 $2n$ 个。

扣出所有的本源平方串，维护出以每个点开始的最短平方串，每个点结束的最短平方串，然后就是一个类似于二维数点的过程。

注意：某个点开始和结尾可能有多个串，因此扣出本源平方后要预处理一下。

## J Cool Slogans

给定一个串 $S$，构造一个最长的序列 $s_1,s_2,\dots,s_k$，满足 $\forall 1\le i \le k$，$s_i$ 是 $S$ 的子串，且 $\forall 2 \le i \le k$，$s_{i-1}$ 在 $s_i$ 中出现至少两次。

首先，需要注意到一个结论：可以构造一个序列，每个都是后一个的后缀，成为满足题意的最长序列。

证明：如果 $s_k$ 不是 $s_{k+1}$ 的后缀，考虑 $s_k$ 在 $s_{k+1}$ 的最后一个出现位置，把 $s_{k+1}$ 的这一部分全部削掉。显然，削过的 $s_{k+1}$ 在 $s_{k+2}$ 仍然出现至少两次，同样考虑最后一个出现位置进行处理，直到最后一个。

考虑后缀自动机的结点（等价类），发现有一个性质：等价类中不可能有一个串在最长的串中出现两次。

证明：假设 $A$ 是某个等价类中的最长串，$B$ 属于这个等价类，且 $B$ 在 $A$ 中出现两次。那么根据等价类的定义，$A$ 也会在 $B$ 所有位置出现，这会导致 $A$ 串无限向左延伸，直到到达最左端。那么此时，$B$ 会在 $A$ 中某个位置出现，而 $A$ 不可能继续延伸，因此 $A$ 和 $B$ 的结束位置集合不同，矛盾。

因此，一个等价类集合，我们只需要考虑一个串即可，不妨考虑最长串，在 fail 树上从上往下贪心地转移。

注意：不能从底往上，因为序列不一定在 fail 树上是一段连续的子孙，可能会出现从某个祖先转移下来的情况。

## K Om Nom and Necklace

对于输入串的每个前缀，回答能够构造一种方案使得前缀是 $ABABABA$ 型，$k+1$ 个 $A$ 串，$k$ 个 $B$ 串，其中 $A,B$ 都允许为空。

建出 $fail$ 树，维护出每个前缀的 $border$ 序列，也就是周期序列，记前缀长度为 $i$，判断是否有周期 $p$ 满足 $kp \le i \le (k+1)p$。

TODO：貌似有线性简单的做法。

## L The Problem to Slow Down You

求两个串公共回文串个数。

建出两个串的回文树，在两棵树上同时 dfs 即可。

## M Find the Period

UnSolved.

见 2019 年国家集训队论文。

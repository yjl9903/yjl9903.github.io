---
mathjax: true
title: 2019 ICPC 徐州现场赛 复现
tags:
  - Trainings
categories:
  - 训练
date: 2019-12-07 17:05:06
---

| rank | solved |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K  |  L  |  M  |
| :--: | :----: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  ?   |   8    |  O  |  .  |  O  |  .  |  O  |  O  |  .  |  O  |  .  |  O  |  .  |  O  |  O  |

<!--more-->

# A

Solved by Henry.

# C

Solved by Henry.

# E

Solved by Henry.

# F

Solved by Forsaken.

# H

Solved by XLor and Forsaken.

假设 $[0,x]$ 能够构造，询问 $[0,x+1]$ 的权值和为 $S$，那么 $[0,S]$ 必能构造，且 $S$ 每次至少翻一倍。

套个树状数组套主席树，时间复杂度 $O(n\log^3n)$。

# J

Solved by Henry.

如果 $n$ 是奇数，显然存在欧拉回路，截断欧拉回路就是答案。

如果 $n$ 是偶数，想先构造 $n/2$ 条路径，第i条路径连接 $2i-1$ 和 $2i$ 两个点，且路径的长度都要不同。
我选择 $i \bmod 2=1$ 时，路径长度为 $2i-1$ ,依次连接 $2i-1 \to 1 \to 2i \to 2 \to \dots \to 2i-1$ 然后直接连到 $2i$ , $i\bmod2=1$ 时，路径长度为 $2i-2$ ,依次连接 $2i-1 \to 1 \to 2i \to 2 \to \dots \to 2i$ 

显然，每条路径最多占用每个点2个度数，且每条边连接的点标号一定是$2i$或$2i-1$和一个小于$2i$的点, 保证不和之前连接的边重复。构造这样$n/2$条路径之后，剩下的所有点度数为偶数，跑欧拉回路切断得到剩下的答案。

# L

Solved by XLor.

模板题。

# M

Solved by XLor.

两棵树合并后的重心，在原来两个重心的路径上。

dfs，从下往上，重心往上爬。

# 代码

## J

```c++
#include<bits/stdc++.h>
using namespace std;
const int N=1003;
int n;
bool vis[N][N];
int ans[N*N/2],cnt;
int tmpans[N][N];

void dfs(int x){
	for(int i=1; i<=n; i++){
		if(!vis[x][i]){
			vis[x][i]=vis[i][x]=1;
			dfs(i);
		}
	}
	ans[cnt++]=x;
	return;
}

int main(){
	scanf("%d",&n);
	for(int i=1; i<=n; i++) vis[i][i]=1;
	if(n%2){
		dfs(n);
		int p=0;
		for(int i=1; i<n; i++){
			for(int j=0; j<i; j++)
				printf("%d ",ans[p++]);
			printf("%d\n",ans[p]);
		}
	}
	else{
		int p=1;
		for(int i=1; i<n; i+=2){
			for(int j=0; j<p; j++){
				if(j%4==0) tmpans[p][j]=i;
				else if(j%4==2) tmpans[p][j]=i+1;
				else tmpans[p][j]=(j+1)/2;
			}
			tmpans[p][p]=i+1;
			for(int j=0; j<p; j++){
				vis[tmpans[p][j]][tmpans[p][j+1]]=1;
				vis[tmpans[p][j+1]][tmpans[p][j]]=1;
			}
			if(p%4==1){
				p++;
			}
			else{
				p+=3;
			}
		}
		dfs(n);
		p=0;
		for(int i=1; i<n; i++){
			if(tmpans[i][0]){
				for(int j=0; j<=i; j++){
					printf("%d%c",tmpans[i][j],j==i?'\n':' ');
				}
			}
			else{
				for(int j=0; j<i; j++)
					printf("%d ",ans[p++]);
				printf("%d\n",ans[p]);
			}
		}
	}
	return 0;
}
```
---
mathjax: true
title: 线性基
date: 2019-02-13 00:38:58
tags:
- Math
categories:
- 数学
---

# 概念

[Menci](https://oi.men.ci/linear-basis-notes/)

# 模板

```c++
struct LinearBase {
    static const int maxl = 63;
    ll a[maxl + 5];
    int cnt;
    LinearBase() { cnt=0; ms(a, 0); }
    void clear() { cnt=0; ms(a, 0); }
    void insert(ll x) {
        for (int i = maxl - 1; i >= 0; i--) {
            if (x & (1ll << i)) {
                if (a[i]) x ^= a[i];
                else {
                    for (int k = 0; k < i; k++) 
                        if (x & (1ll << k)) x ^= a[k];
                    for (int k = i + 1; k < maxl; k++) 
                        if (a[k] & (1ll << i)) a[k] ^= x;
                    a[i] = x; cnt++;
                    return ;
                }
            }
        }
    }
    bool check(ll x) {
        for (int i = maxl - 1; i >= 0; i--) {
            if (x >> i & 1) {
                if (a[i]) x ^= a[i];
                else return false;
            }
        }
        return true;
    }
    ll qmax(int x) {
        ll res = x;
        for(int i = maxl - 1 ; i >= 0; i--) {
            if ((res ^ a[i]) > res) res ^= a[i];
        }
        return res;
    }
    // #define QUERY_KTH
    #ifdef QUERY_KTH
    vector<ll> v;
    void init_kth() {
        v.clear();
        for (int i = 0; i < maxl; i++) if (a[i]) v.push_back(a[i]);
    }
    ll query(ll k){
        if (v.size() != n) k--;
        if (k >= (1ll << v.size())) return -1;
        ll ans = 0;
        for (int i = 0; i < v.size(); i++) if (k & (1ll << i))
            ans ^= v[i];
        return ans;
    }
    #endif
};
```

# 区间线性基

```c++
struct LinearBase {
    static const int M = 30;
    int a[M + 1], pos[M + 1];
    void clear() {
        ms(a, 0); ms(pos, 0);
    }
    LinearBase() { 
        clear();
    }
    int insert(int x, int id = 0) {
        for (int i = M; i >= 0; i--) {
            if (x >> i & 1) {
                if (a[i]) {
                    if (id > pos[i]) swap(id, pos[i]), swap(x, a[i]);
                    x ^= a[i];
                } else {
                    a[i] = x; pos[i] = id;
                    return true;
                }
            }
        }
        return false;
    }
    int query(int x, int l) {
        int ans = x;
        for (int i = M; i >= 0; i--) {
            if (pos[i] < l) continue;
            if ((ans ^ a[i]) >= ans) ans ^= a[i];
        }
        return ans;
    }
};
```

# 线性基求交

```c++
LinearBase intersect(const LinearBase& A, const LinearBase& B) {
	LinearBase all, C, D;
	for (int i = maxl - 1; i >= 0; i--) {
		all.a[i] = A.a[i];
		D.a[i] = 1ll << i;
	}
	for (int i = maxl - 1; i >= 0; i--) {
		if (B.a[i]) {
			ll v = B.a[i], k = 0;
			bool can = true;
			for (int j = 60; j >= 0; j--) {
				if (v & (1ll << j)) {
					if (all.a[j]) {
						v ^= all.a[j];
						k ^= D.a[j];
					} else {
						can = false;
						all.a[j] = v;
						D.a[j] = k;
						break;
					}
				}
			}

			if (can) {
				ll v = 0;
				for (int j = 60; j >= 0; j--) {
					if (k & (1ll << j)) {
						v ^= A.a[j];
					}
				}
				C.insert(v);
			}
		}
	}
	return C;
}
```

<!--more-->

# Codeforces 662A Gambling Nim

有 $n$ 张牌，每张牌正反面分别一个数字，每张牌随机正反面，得到一个 $Nim$ 游戏的石子数序列，求先手必胜的概率。

题目等价于询问 $a_i \oplus b_i$ 有多少子集异或和等于 $\oplus a_i$。

线性基大小为 $k$，可以表示成 $2^k$ 个不同数，每个数出现次数都相同。

因此，如果 $a_i \oplus b_i$ 建出的线性基能够构造出 $\oplus a_i$，那么就概率即为 $2^k-1 \over 2^k$，否则先手必胜。
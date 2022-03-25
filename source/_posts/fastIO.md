---
title: 读入挂
date: 2018-08-15 11:06:50
tags: other
categories: 乱七八糟
comments: false
---

**请用文件读入!**

{% codeblock lang:cpp %}
struct fastIO{
	char s[100000]; int it,len;
	fastIO(){it = len = 0;}
	inline char get(){
		if (it < len) return s[it++];
    it = 0; len = fread(s, 1, 100000, stdin);
		if (len == 0) return EOF;
    else return s[it++];
	}
	bool notend(){
		char c = get();
		while(c == ' ' || c == '\n') c = get();
		if (it > 0) it--;
		return c != EOF;
	}
}_buff;
inline ll getNum(){
	ll r = 0; bool ng = 0; 
  char c = _buff.get();
	while (c != '-' && (c < '0' || c > '9')) c = _buff.get();
	if (c == '-') ng = 1, c = _buff.get();
	while (c >= '0' && c <= '9') r = r * 10 + c - '0', c = _buff.get();
	return ng ? -r : r;
}
{% endcodeblock %}
---
title: 表达式计算
date: 2018-08-14 16:20:43
tags: other
categories: 乱七八糟
comments: false
---

# 优先级

乘除 > 加减 > 左括号( > 右括号)

# 计算过程

维护一个操作数栈，一个操作符栈。

每次遇到一个数字或 '(' 压栈。

对于 +-*/ 符号，如果当前符号优先级小于等于栈顶符号，则进行计算，弹出栈顶，直到优先级大于栈顶符号，将当前符号压栈。

对于 ) 符号，不断出栈计算，直到遇到第一个 (。

{% codeblock lang:cpp %}
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <stack>
#define ms(a,b) memset(a,b,sizeof(a))
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

char s[maxn];

int get(char ch){
    if (ch == '+' || ch == '-') return 1;
    if (ch == '*' || ch == '/') return 2;
    if (ch == '(') return 0;
    return -1;
}
int cmp(char x, char y){
    return get(x) <= get(y);
}

double cal(){
    stack<double> num; stack<char> ope;
    int len = strlen(s); double ans = 0;
    for (int i = 0; i < len; i++){
        if (s[i] >= '0' && s[i] <= '9') num.push(double(s[i] - '0'));
        else{
            if (s[i] == '(') {
                ope.push(s[i]); continue;
            }
            int flag = 0;
            while (!ope.empty() && cmp(s[i], ope.top())){
                char ch = ope.top(); 
                if (s[i] == ')'){
                    if (flag) break;
                    if (ch == '(') flag = 1;
                }
                ope.pop();
                if (ch == '*'){
                    double x = num.top(); num.pop();
                    double y = num.top(); num.pop();
                    num.push(x * y);
                }
                if (ch == '/'){
                    double x = num.top(); num.pop();
                    double y = num.top(); num.pop();
                    num.push(y / x);
                }
                if (ch == '+'){
                    double x = num.top(); num.pop();
                    double y = num.top(); num.pop();
                    num.push(x + y);
                }
                if (ch == '-'){
                    double x = num.top(); num.pop();
                    double y = num.top(); num.pop();
                    num.push(y - x);
                }
            }
            if (s[i] != ')') ope.push(s[i]);
        }
    }
    return ans = num.top();
}

int main(){
    int T; scanf("%d", &T);
    while (T--){
        scanf("%s", s + 1);
        int len = strlen(s + 1); s[0] = '(', s[len + 1] = ')'; s[len + 2] = '\0';
        printf("%.2lf\n", cal());
    }
    return 0;
}
{% endcodeblock %}

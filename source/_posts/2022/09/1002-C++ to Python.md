---
title: 1002-C++ to Python
mathjax: true
date: 2022-09-01 00:00:00
tags:
---
# 1002-C++ to Python

作者: 叶继禹
来源: 杭电杯超级联赛2
算法: 字符串, 语法
补完: Yes
完成时间: July 31, 2022

# 题解

去除字母下划线冒号, 保留数字逗号和括号即可

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
int T;
void solve(){
    string a;
    cin>>a;
    int len=a.length();
    for(int i=0;i<len;i++)
        if(!isalpha(a[i])&&(a[i]!='_')&&(a[i]!=':'))
            printf("%c",a[i]);
    printf("\n");
}
int main(){
    cin>>T;
    while(T--)solve();
    return 0;
}
```
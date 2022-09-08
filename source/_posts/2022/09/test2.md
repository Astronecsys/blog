---
title: test2
mathjax: true
date: 2022-09-08 12:50:27
tags:
---

# I-Chiitoitsu

作者: 叶继禹
完成时间: July 22, 2022
来源: 牛客多校训练营1
算法: 动态规划, 概率DP
补完: Yes
题目链接: https://ac.nowcoder.com/acm/contest/33186/I

## 解题

最优策略相当于开了透视,保留的手牌一定是最快凑成对子的,把第二张牌来得晚的丢掉.

所以我们具体手牌是什么不重要,只要知道已经凑齐了几个对子就好了

此时对于保留在手上的待匹配牌,还有三张牌在牌池里.

设牌池还剩$i$张牌,还有$j$张牌需要凑对子,$f[i][j]$为距结束步数

则有摸下一张牌时,可以匹配的概率为$\frac{3*j}{i}$,不可以匹配的概率为$\frac{i-3*j}{i}$

因为初始手牌为$13$张,其中有$13-j$张牌是对子,则在游戏结束前$j$只能为奇数

当$j=0$时,

所有牌都得到匹配,游戏结束,距游戏结束还有 $0$回合

$$
\begin{align}
f[i][0]=0
\end{align}
$$

当$j=1$时,

摸完一张牌之后牌池还剩$i-1$张牌,

若匹配则消不匹配的牌,还有$j-1$张待匹配的牌,

若不可以匹配则丢摸到的这张牌,还有$j$张待匹配的牌

$$
\begin{align}
f[i][j]&=1\\
&+\frac{3*j}{i}*f[i-1][j-1]&匹配\\
&+\frac{i-3*j}{i}*f[i-1][j]&不匹配
\end{align}
$$

当$j>1$时,

摸完一张牌之后牌池还剩$i-1$张牌,

若匹配则消不匹配的牌同时丢一张不能匹配的牌,还有$j-2$张待匹配的牌,

若不可以匹配则丢摸到的这张牌,还有$j$张待匹配的牌

$$
\begin{align}
f[i][j]&=1\\
&+\frac{3*j}{i}*f[i-1][j-2]&匹配\\
&+\frac{i-3*j}{i}*f[i-1][j]&不匹配
\end{align}
$$

跑完之后直接拿数据访问$f[i][j]$即可

## 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll P = 1e9+7;
int t=1;
ll f[137][14];
ll qpow(ll x,ll a){
    ll res=1;
    while(a){
        if(a&1)res=res*x%P;
        x= x*x%P;
        a>>=1;
    }
    return res;
}
void Init() {
    for(int j=1;j<=13;j+=2){
        for(int i=1;i<=136;i++){
            if(j==1)f[i][j]=(1+(3*j)*qpow(i,P-2)%P*f[i-1][j-1]%P+(i-3*j)*qpow(i,P-2)%P*f[i-1][j]%P)%P;
            else    f[i][j]=(1+(3*j)*qpow(i,P-2)%P*f[i-1][j-2]%P+(i-3*j)*qpow(i,P-2)%P*f[i-1][j]%P)%P;
        }
    }
}
void solve(){
    char s[26];
    scanf("%s",&s);
    int pp[5][10];
    for(int i=1;i<5;i++){
        for(int j=1;j<10;j++){
            pp[i][j]=0;
        }
    }
    int cnt=0;
    for(int i=0;i<26;i+=2){
        switch(s[i+1]){
        case 'm':pp[1][s[i]-'0']++;break;
        case 'p':pp[2][s[i]-'0']++;break;
        case 's':pp[3][s[i]-'0']++;break;
        case 'z':pp[4][s[i]-'0']++;break;
        default:break;
        }
    }
    for(int i=1;i<5;i++){
        for(int j=1;j<10;j++){
            if(pp[i][j]>1)cnt++;
        }
    }
    printf("Case #%d: %lld\n",t++,f[123][13-cnt*2]);
}
int main(){
    Init();
    int T;
    cin>>T;
    while(T--)solve();
    return 0;
}
```
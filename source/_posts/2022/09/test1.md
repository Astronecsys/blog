---
title: test1
date: 2022-09-07 09:11:17
tags:
mathjax: true
---

# D-Link with Game Glitch

作者: 叶继禹
完成时间: July 25, 2022
来源: 牛客多校训练营2
算法: 图论
补完: Yes
题目链接: https://ac.nowcoder.com/acm/contest/33187/D

## 算法

①二分答案+SPFA判负环

②Karp的最小平均权重环路算法

## 题解

这里考虑法②

题目有对于配方$i$有: 以$a_i$个物品 $b_i$为原料,制造$c_i$个物品$d_i$为产品

$$
a_i\times b_i\to c_i \times d_i 
$$

可以理解为对于配方$i$有: 以$\frac{a_i}{c_i}$个物品 $b_i$为原料,制造$1$个物品$d_i$为产品

$$
\frac{a_i}{c_i}\times b_i \to d_i
$$

对于连续$k$个配方可以由配方的系数乘积从原料制造出产品

$$
\prod_{i}^k\frac{a_i}{c_i} 原料\to 产品
$$

若某物品经过$k$道配方, 最终可以制造自己, 称为一循环配方

$$
\prod_{i}^k\frac{a_i}{c_i} 物品\to 物品
$$

以$n$种物品为点, $m$种配方为边, $\frac{a_i}{c_i}$为边权建立有向图, 循环配方表现为环

设循环配方中任意物品原有数量为$x$,个 则每次循环后可变为为$\frac {x}{\prod_{i}^k\frac{a_i}{c_i}}$个,

要使物品不会变多, 则必须$x≥\frac {x}{\prod_{i}^k\frac{a_i}{c_i}}$, 也即

$$
\prod_{i}^k\frac{a_i}{c_i}≥1
$$

引入生产修正参数$w$

$$
a_i\times b_i\to w\times c_i \times d_i 
$$

可以理解为

$$
\frac{a_i}{wc_i}\times b_i \to d_i
$$

可推得

$$
\prod_{i}^k\frac{a_i}{wc_i} 物品\to 物品
$$

将$w$提出有

$$
\frac1{w^k}\prod_{i}^k\frac{a_i}{c_i} 物品\to 物品
$$

重复上文推论

以$n$种物品为点, $m$种配方为边, $\frac{a_i}{wc_i}$为边权建立有向图, 循环配方表现为环

设循环配方中任意物品原有数量为$x$,个 则每次循环后可变为为$\frac {x}{\frac1{w^k}\prod_{i}^k\frac{a_i}{c_i}}$个,

要使物品不会变多, 则必须$x≥\frac {x}{\frac1{w^k}\prod_{i}^k\frac{a_i}{c_i}}$, 也即

$$
\prod_{i}^k\frac{a_i}{c_i}≥w^k
$$

对于不同的环有不同的$\prod_{i}^k\frac{a_i}{c_i}$, 即要求在每一个环中都满足上式,即

$$
\min(\prod_{i}^k\frac{a_i}{c_i})≥w^k
$$

且要求$w$取最大, 则要求

$$
\min(\prod_{i}^k\frac{a_i}{c_i})=w^k
$$

化简得

$$
w=\min\left(\sqrt[k]{\prod_{i}^k\frac{a_i}{c_i}}\right)
$$

其中连乘可用对数处理

$$
\ln(\prod_{i}^k\frac{a_i}{c_i})=\sum_i^k\ln(\frac{a_i}{c_i})
$$

化简得

$$
\prod_{i}^k\frac{a_i}{c_i}=e^{\sum_i^k\ln(\frac{a_i}{c_i})}
$$

即要求

$$
w^k=e^{\min(\sum_i^k\ln(\frac{a_i}{c_i}))}
$$

最终

$$
w=\min(\sqrt[k]{e^{\sum_i^k\ln(\frac{a_i}{c_i})}})
$$

$$
w=\exp\left(\min(\frac{\sum_i^k\ln(\frac{a_i}{c_i})}{k})\right)
$$

则理解为求环的最小平均权重

以$\ln(\frac{a_i}{c_i})$为边权建图$G(V,E)$

有$Karp的最小平均权重环路算法$

让$F[k][v]$为从任意点出发,经过$k$条边到达$v$点的最短距离, 对每条边$u\to v$ 有

$$
F[k][v]=\min_{e\in E}\{F[k-1][u]+w[u][v]\}
$$

$$
ans = \min_{v\in V}\{\max_{0≤k≤n-1}\{\frac{F[n][v]-F[k][v]}{n-k}\}\}
$$

### 参考

****[有向图中的最小平均权值回路](https://blog.csdn.net/qq_30361651/article/details/107801468)****

## 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 2005;
int n,m,b,d;
double a,c,ans;
double dp[maxn][maxn];
struct edge {
    int u;
    int v;
    double w;
};
vector<edge> edges;
int main(){
    cin>>n>>m;
    for(int i=1;i<=m;i++){
        cin>>a>>b>>c>>d;
        edges.push_back({b,d,log(a/c)});
    }
    for (int i=1;i<=n;i++) {
        for (auto [u,v,w] : edges) {
            dp[i][v] = min(dp[i][v],dp[i-1][u]+w);
        }
    }
    for(int v=1;v<=n;v++){
        double t = -INFINITY;
        for(int k=0;k<n;k++){
            t = max(t,(dp[n][v]-dp[k][v])/(n-k));
        }
        ans = min(t,ans);
    }
    printf("%.15lf\n",exp(ans));
    return 0;
}
```
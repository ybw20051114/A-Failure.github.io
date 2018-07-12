﻿---
layout:     post
title:      "[USACO09MAR]地震损失2Earthquake Damage 2"
date:       2017-12-21
author:     "DieSheep"
header-img: "img/used/64361.jpg"
catalog: true
tags:
    - 网络流
---
# [题目](https://www.luogu.org/problemnew/show/P2944)
## 题目描述
>Wisconsin has had an earthquake that has struck Farmer John's farm! The earthquake has damaged some of the pastures so that they are unpassable. Remarkably, none of the cowpaths was damaged.

>As usual, the farm is modeled as a set of P (1 <= P <= 3,000)

>pastures conveniently numbered 1..P which are connected by a set of C (1 <= C <= 20,000) non-directional cowpaths conveniently

>numbered 1..C. Cowpath i connects pastures a_i and b_i (1 <= a_i <= P; 1 <= b_i <= P). Cowpaths might connect a_i to itself or perhaps might connect two pastures more than once. The barn is located in pasture 1.

>A total of N (1 <= N <= P) cows (in different pastures) sequentially contacts Farmer John via moobile phone with an integer message report_j (2 <= report_j <= P) that indicates that pasture report_j is undamaged but that the calling cow is unable to return to the barn from pasture report_j because she could not find a path that does not go through damaged pastures.

>After all the cows report in, determine the minimum number of

>pastures that are damaged.

>地震袭击了威斯康星州，一些牧场被摧毁了.

>一共有P个牧场.由C条双向路连接.两个牧场间可能有多条路.一条路也可能连接相同的牧场.牛棚坐落在牧场1.

>N (1 <= N <= P) 只奶牛打来了求救电话，说她们的农场没有被摧毁，但是已经无法到达牛棚. 求出最少可能有多少牧场被摧毁.

## 输入输出格式
### 输入格式：
>* Line 1: Three space-separated integers: P, C, and N

>* Lines 2..C+1: Line i+1 describes cowpath i with two integers: a_i and b_i

>* Lines C+2..C+N+1: Line C+1+j contains a single integer: report_j

### 输出格式：
>* Line 1: One number, the minimum number of damaged pastures.

## 输入输出样例
### 输入样例#1： 
>5 5 2 

>1 2 

>2 3 

>3 5 

>2 4 

>4 5 

>4 

>5 

### 输出样例#1： 
>1 

## 说明
>Only pasture 2 being damaged gives such a scenario.

问题转化：已确定几个点不割，问最少割几个点使图分成两部分

考虑最小割，因为最小割是割边

所以把点转换为边，即把每个点分为入点和出点

确定的点入点出点之间连边为无穷大，保证最小割集合中不包含这个点

并且与超汇点连边为无穷大，保证经过确定点

未确定点入点出点之间连边为1，最小割集合中可以包含这个点

然后就是慢慢建已知边啦

注意是双向边，还有1点也是确定点

代码：
```
# include<iostream>
# include<cstdio>
# include<cstring>
# include<queue>
using namespace std;
const int t=500000;
struct q{
    int x,y,dis;
}c[6000001];
int p,C,n,num;
int h[600001],d[600001];
bool use[3001];
void add(int x,int y,int dis)
{
    c[num].x=h[x];
    c[num].y=y;
    c[num].dis=dis;
    h[x]=num++;
}
bool bfs()
{
    queue<int> qu;
    qu.push(0);
    memset(d,0,sizeof(d));
    d[0]=1;
    while(!qu.empty())
    {
        int tt=qu.front();
        qu.pop();
        for(int i=h[tt];i;i=c[i].x)
          if(!d[c[i].y]&&c[i].dis)
          {
              d[c[i].y]=d[tt]+1;
              qu.push(c[i].y);
          }
    }
    return d[t];
}
int dfs(int x,int dix)
{
    if(x==t) return dix;
    int sum=0;
    for(int i=h[x];i;i=c[i].x)
      if(d[c[i].y]==d[x]+1&&c[i].dis)
      {
          int dis=dfs(c[i].y,min(dix,c[i].dis));
          if(dis)
          {
              sum+=dis;
              dix-=dis;
              c[i].dis-=dis;
              c[i^1].dis+=dis;
              if(!dix) break;
        }
      }
    return sum;
}
int dinic()
{
    int tot=0;
    while(bfs())
    tot+=dfs(0,1e8);
    return tot;
}
int main()
{
    scanf("%d%d%d",&p,&C,&n);
    add(1,0,0),add(0,1,1e8),add(1+p,1,0),add(1,1+p,1e8);
    for(int i=1;i<=C;i++)
      {
          int x,y;
          scanf("%d%d",&x,&y);
          add(x,y+p,0);
          add(y+p,x,1e8);
          add(y,x+p,0);
          add(x+p,y,1e8);
      }
    for(int i=1;i<=n;i++)
      {
          int x;
          scanf("%d",&x);
          use[x]=1; 
          add(x+p,x,0);
          add(x,x+p,1e8);
        add(t,x+p,0);
          add(x+p,t,1e8);
      }
    for(int i=2;i<=p;i++)
      if(!use[i])
      {
          add(i+p,i,0);
          add(i,i+p,1);
      }
    printf("%d",dinic());
    return 0;
}
```

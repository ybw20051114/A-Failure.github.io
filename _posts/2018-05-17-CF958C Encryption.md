---
layout:     post
title:      "CF958C Encryption"
date:       2018-05-17
author:     "DieSheep"
header-img: "img/qwq.jpg"
catalog: true
tags:
    - 动态规划
    - 树状数组
---
>学校github终于能上了。。。

# [题目(C3)](http://codeforces.com/problemset/problem/958/C3)

（都是英文就不放题面了）

## C1
这个简单吧。。。瞎暴力就能过

代码：
```
# include<iostream>
# include<cstring>
# include<cstdio>
# define LL long long
using namespace std;
const int MAX=1e5+1;
int n,m;
LL sum[MAX];
int read()
{
	int x=0;
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
int main()
{
	n=read(),m=read();
	for(int i=1;i<=n;++i)
	  sum[i]=sum[i-1]+read();
	int maxn=0;
	for(int i=1;i<n;++i)
	  {
	  	int l=sum[i]%m,r=(sum[n]-sum[i])%m;
	  	maxn=max(maxn,l+r);
	  }
	printf("%d",maxn);
	return 0;
}
```

## C2
一个简单的dp，但是会TLE。。。

似乎有多种方法降低复杂度，我用的树状数组，维护前面的最小值就行了

代码：
```
# include<iostream>
# include<cstring>
# include<cstdio>
# include<algorithm>
# define LL long long
# define mid (l+r>>1)
using namespace std;
const int MAX=2e4+1;
int s[51][MAX],pos[51][MAX];
int n,k,m,tot;
LL sum[MAX];
LL f[MAX][51];
int read()
{
	int x=0;
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
int lowbit(int x)
{
	return x&(-x);
}
void change(int k,int x,int dis)
{
	for(int i=x;i<=n;i+=lowbit(i))
	  if(s[k][i]<=dis) s[k][i]=dis,pos[k][i]=x;
}
pair<int,int> ask(int k,int x)
{
	pair<int,int> ans=make_pair(0,0);
	for(int i=x;i;i-=lowbit(i))
	  if(ans.first<=s[k][i]) ans.first=s[k][i],ans.second=pos[k][i];
	return ans;
}
int main()
{
	n=read(),k=read()-1,m=read();
	for(int i=1;i<=n;++i)
	  sum[i]=sum[i-1]+read(),f[i][0]=sum[i]%m,change(0,i,f[i][0]);
	for(int i=2;i<=n;++i)
	  {
	  	for(int l=1;l<=k;++l)
	      {
	      	pair<int,int> x=ask(l-1,i-1);
	      	if(!x.second) x.second=i;
	      	f[i][l]=x.first+(sum[i]-sum[x.second])%m;
	      	change(l,i,f[i][l]);
		  }
	  }
	cout<<f[n][k];
	return 0;
}
```

## C3
hard不会了qwq。。。[题解](https://blog.csdn.net/u013534123/article/details/80022683)

令s[i]=(s[i]+s[i-1])%p

则可以分两种情况讨论：

若s[i]>s[j]，结果就是s[i]-s[j]

若s[i]<=s[j]，结果就是s[i]-s[j]+p

所以用两个树状数组，一个维护ans-s[j]，一个维护ans-s[j]+p

代码（完全抄袭）：
```
# include<iostream>
# include<cstring>
# include<cstdio>
using namespace std;
const int MAXN=5e5+1,MAX=101;
int n,k,p;
int f[MAX],a[MAXN];
int s[2][MAX][MAX];
int min(int x,int y)
{
	return x<y?x:y;
}
int lowbit(int x)
{
	return x&-x;
}
void change(int op,int k,int x,int dis)
{
	for(int i=x+1;i<=p+1;i+=lowbit(i))
	  s[op][k][i]=min(s[op][k][i],dis);
}
int ask(int op,int k,int x)
{
	int ans=1e8;
	for(int i=x+1;i;i-=lowbit(i))
	  ans=min(ans,s[op][k][i]);
	return ans;
}
int read()
{
	int x=0;
	char ch=getchar();
	for(;!isdigit(ch);ch=getchar());
	for(;isdigit(ch);x=x*10+ch-48,ch=getchar());
	return x;
}
int main()
{
	n=read(),k=read(),p=read();
	for(int i=1;i<=n;++i)
	  a[i]=(read()+a[i-1])%p;
	memset(s,1,sizeof(s));
	change(0,0,p+1,0),change(1,0,0,0);
	for(int i=1;i<=n;++i)
	  {
	  	int End=min(i,k);
	  	for(int j=1;j<=End;++j)
	  	  f[j]=min(ask(0,j-1,a[i]),ask(1,j-1,p-a[i]))+a[i];
	  	for(int j=1;j<=End;++j)
	  	  {
	  	  	if(f[j]>1e7) continue;
	  	  	change(0,j,a[i],f[j]-a[i]),change(1,j,p-a[i],f[j]-a[i]+p);
		  }
	  }
	printf("%d",f[k]);
	return 0;
}
```
233

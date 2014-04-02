---
author: admin
comments: true
date: 2011-08-01 12:59:20+00:00
layout: post
slug: spoj-untitle1
title: SPOJ UNTITLE1
wordpress_id: 465
categories:
- 解题报告
tags:
- spoj
- 解题报告
---

很久没有写题解了，为了攒点rp，写一篇吧……
题目大意：给你一个序列（N&lt;=50000），给某个区间的数都加上一个值，询问尾部在x～y的最大前缀和。

那么我们先根号N分段，每一段维护一个凸包支持询问这一段的最大前缀和。

即Max P=sum[i]+add*i，i是这个区间的第i个位置，sum[i]表示这个区间前i项和，add表示这个区间整个增加的值。

那么设y=sum[i],x=i，y=-add*i+P，add在询问时是一个常量，我们可以想象有一个斜率为-add的直线从上往下降落，最先碰到的点就是最大化P的最优解。那么很显然要维护一个上凸包，询问时二分碰到的点就可以了。

总结一下：对于修改操作，中间的段直接修改标号，两边的直接修改sum数组求凸包。注意求凸包时按照x递增的顺序加点，使用graham维护可以不用排序。对于询问操作，两边直接暴力枚举，中间的就用前面的方法二分找出第一个斜率小于-add的直线，取左端点为当前候选答案就可以了。

&nbsp;
<pre lang='CPP'>
#include <cstdio>
#include <cmath>
#include <vector>
#include <algorithm>
using namespace std;

typedef long long Int;
const Int MAXN=100005;
const Int MAXM=1000;//ceil(sqrt(MAXN));
const Int oo=~0ull>>1;
#define pb push_back

struct Point
{
	Int x,y;
	Point (Int _x=0,Int _y=0):x(_x),y(_y){}
	bool operator < (const Point &b) const
	{
		return x<b.x || (x==b.x && y<b.y);
	}
	Int operator * (const Point &b) const
	{
		return (Int)x*b.y-(Int)b.x*y;
	}
	Point operator - (const Point &b) const
	{
		return Point (x-b.x,y-b.y);
	}
}P[MAXM];

Point *hull[MAXM];
struct Block
{
	Int l,r,size,add,sum[MAXM],ind[MAXM];
	double slope[MAXM];
	Int top;
	void update()
	{
		for(Int i=0;i<size;i++)
			P[i].x=i+1,P[i].y=sum[i+1];
		gethull(size);
	}
	Int getsum(Int t=-1)
	{
		if (t==-1)
			return sum[size]+add*size;
		else
			return sum[t]+add*t;
	}
	void addval(Int x,Int y,Int k)
	{
		x=x-l+1,y=y-l+1;
		for(Int i=x,j=k;i<=y;i++,j+=k)
			sum[i]+=j;
		for(Int i=y+1,j=(y-x+1)*k;i<=size;i++)
			sum[i]+=j;
		if (add!=0)
		{
			for(Int i=1,j=add;i<=size;i++,j+=add)
				sum[i]+=j;
			add=0;
		}
		update();
	}
	Int getmax()
	{
		Int pos=lower_bound(slope,slope+top,add)-slope;
		return getsum(ind[pos]);
	}
	void gethull(Int size)
	{
		if (size>=2)
		{
			hull[0]=&P[0];
			hull[1]=&P[1];
			top=2;
			for(Int i=2;i<size;i++)
			{
				while(top>=2 && (*hull[top-1]-*hull[top-2])*(P[i]-*hull[top-1])>=0)
					top--;
				hull[top++]=&P[i];
			}
		}
		else
		{
			hull[0]=&P[0];
			top=1;
		}
		slope[0]=-oo;
		for(Int i=1;i<top;i++)
		{
			slope[i]=-(hull[i]->y-hull[i-1]->y)/double(hull[i]->x-hull[i-1]->x);
			ind[i]=hull[i-1]-P+1;
		}
		slope[top]=oo;
		ind[top]=hull[top-1]-P+1;
		top++;
	}
}block[MAXM];

int N;
int A[MAXN],belong[MAXN];
void init()
{
	scanf("%d",&N);
	Int size=sqrt(N);
	for(Int i=0;i<N;i++)
		scanf("%d",A+i);
	for(Int j=0,i=0;i<N;j++)
	{
		Block &now=block[j];
		now.size=min(size,N-i);
		now.l=i;
		now.sum[0]=0;
		for(Int k=1;k<=now.size;k++)
		{
			now.sum[k]=now.sum[k-1]+A[i];
			belong[i]=j;
			i++;
		}
		now.r=i-1;
		now.add=0;
		now.update();
	}
}

void add(Int x,Int y,Int k)
{
	Int l=belong[x],r=belong[y];
	for(Int i=l+1;i<r;i++)
		block[i].add+=k;
	if (l==r)
		block[l].addval(x,y,k);
	else
	{
		block[l].addval(x,block[l].r,k);
		block[r].addval(block[r].l,y,k);
	}
}

Int query(Int x,Int y)
{
	Int l=belong[x],r=belong[y];
	Int base=0,ret=-oo,now;
	for(Int i=0;i<l;i++)
		base+=block[i].getsum();
	if (l!=r)
	{
		for(Int i=x-block[l].l+1;i<=block[l].size;i++)
			if ((now=base+block[l].getsum(i))>ret)
				ret=now;
		base+=block[l].getsum();
		for(Int i=l+1;i<r;i++)
		{
			if ((now=base+block[i].getmax())>ret)
				ret=now;
			base+=block[i].getsum();
		}
		for(Int i=1;i<=y-block[r].l+1;i++)
			if ((now=base+block[r].getsum(i))>ret)
				ret=now;
	}
	else
	{
		for(Int i=x-block[l].l+1;i<=y-block[l].l+1;i++)
			if ((now=base+block[l].getsum(i))>ret)
				ret=now;
	}
	return ret;
}

int main()
{
	init();
	int M;
	scanf("%d",&M);
	for(Int i=0;i<M;i++)
	{
		int ind,x,y;
		scanf("%d%d%d",&ind,&x,&y);
		x--,y--;
		if (ind==0)
		{
			int k;
			scanf("%d",&k);
			add(x,y,k);
		}
		else
			printf("%lld\n",query(x,y));
	}
	return 0;
}
</pre>
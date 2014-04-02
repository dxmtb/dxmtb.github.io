---
author: admin
comments: true
date: 2011-03-21 14:08:41+00:00
layout: post
slug: scoi2010stockrading
title: 'Scoi2010 股票交易 '
wordpress_id: 334
categories:
- 解题报告
tags:
- 动态规划
- 单调队列
- 解题报告
---

dp。d[i][j]表示到第i天时（还没开始交易）有j支股票的最大现金。枚举前W天时买或卖了多少支就行了。比如说买：d[i][j]=max{d[i-w-1][k]-(j-k)*AP[i-w-1]}=max{d[i-w-1][k]+k*AP[i-w-1]}-j*AP[i-w-1]。如果k的范围没限制的话，直接记录当前最优值就行了，但有限制的话，我们就把未来可能用的决策放进一个队列里，保证随k的递增d[i-w-1][k]-(j-k)*AP[i-w-1]递减（想想为什么）。因为越靠后越容易取，要是值更优的话，前面的没必要保留。
还要注意一点：第N天卖的情况需要转移到N+W+1天。

    
    
    /*
    ID: dxmtb
    PROG: [Scoi2010]股票交易 
    TIME: 2011.03.21
    STATE: Solved
    MEMO: dp+单调队列
    */
    #include <cstdio>
    #include <deque>
    using namespace std;
    const int MAXN=5005;
    const int oo=0x7fffffff/2;
    
    int N,P,W;
    int AP[MAXN],BP[MAXN],AS[MAXN],BS[MAXN];
    int d[MAXN][MAXN];
    
    int main()
    {
    	freopen("1855.in","r",stdin);
    	scanf("%d%d%d",&N;,&P;,&W;);
    	for(int i=1;i<=N;i++)
    		scanf("%d%d%d%d",AP+i,BP+i,AS+i,BS+i);
    	for(int i=1;i<=P;i++)
    		d[1][i]=-oo;
    	for(int i=2;i<=N+W+1;i++)
    	{
    		int *f=d[i],*g=d[i-W-1];
    		for(int j=0;j<=P;j++)
    			f[j]=d[i-1][j];
    		if (i-W-1>=1)
    		{
    			const int &buy;=AP[i-W-1],&blim;=AS[i-W-1];
    			deque<pair<int,int> > q;
    			q.push_back(make_pair(g[0],0));
    			for(int j=1;j<=P;j++)
    			{
    				while(q.size() && j-q.front().second>blim)
    					q.pop_front();
    				f[j]=max(f[j],q.front().first-j*buy);
    				while(q.size() && q.back().first<g[j]+j*buy)
    					q.pop_back();
    				q.push_back(make_pair(g[j]+j*buy,j));
    			}
    			const int &sell;=BP[i-W-1],&slim;=BS[i-W-1];
    			q.clear();
    			q.push_back(make_pair(g[P]+P*sell,P));
    			for(int j=P-1;j>=0;j--)
    			{
    				while(q.size() && q.front().second-j>slim)
    					q.pop_front();
    				f[j]=max(f[j],q.front().first-j*sell);
    				while(q.size() && q.back().first<g[j]+j*sell)
    					q.pop_back();
    				q.push_back(make_pair(g[j]+j*sell,j));
    			}
    		}
    	}
    	printf("%d\n",d[N+W+1][0]);
    	return 0;
    }
    

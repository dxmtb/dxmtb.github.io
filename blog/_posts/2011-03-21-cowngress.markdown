---
author: admin
comments: true
date: 2011-03-21 23:30:06+00:00
layout: post
slug: cowngress
title: USACO JAN11 奶牛议会
wordpress_id: 342
categories:
- 解题报告
tags:
- 解题报告
---

枚举每个议案是否决还是通过，看是否有合法方案满足。如果否决和通过都没有合法方案，就可以输出不可能了。

    
    
    /*
    ID: dxmtb
    PROG: USACO JAN11 奶牛议会
    TIME: 2011.03.20
    STATE: Solved
    MEMO: 枚举染色
    */
    #include <cstdio>
    #include <cstring>
    #include <queue>
    using namespace std;
    const int MAXN=5000;
    
    struct Node
    {
    	int v;
    	Node *next;
    	Node (int _v,Node *_next):
    		v(_v),next(_next){}
    }*adj[MAXN][2];
    
    inline void addedge(int u1,int u2,int v)
    {
    	adj[u1][u2]=new Node(v,adj[u1][u2]);
    }
    
    int sta[MAXN],cow[MAXN];
    int N,M;
    int a[MAXN],b[MAXN];
    bool va[MAXN],vb[MAXN];
    
    bool bfs(int s)
    {
    	queue<pair<int,int> >q;
    	q.push(make_pair(s,sta[s]));
    	memset(cow,0,sizeof(cow));
    	while(q.size())
    	{
    		pair<int,int> u=q.front();
    		q.pop();
    		for(Node *p=adj[u.first][u.second];p;p=p->next)
    			cow[p->v]=1;
    		for(Node *p=adj[u.first][(u.second^1)];p;p=p->next)
    			if (cow[p->v]!=1)
    			{
    				int nu1=0,nu2=0;
    				int i=p->v;
    				if (a[i]==u.first && va[i]==(u.second^1))
    				{
    					nu1=b[i];nu2=vb[i];
    				}
    				else if (b[i]==u.first && vb[i]==(u.second^1))
    				{
    					nu1=a[i];nu2=va[i];
    				}
    				if (sta[nu1]==(nu2^1))
    					return false;
    				else
    				{
    					if (sta[nu1]==-1)
    					{
    						sta[nu1]=nu2;
    						q.push(make_pair(nu1,nu2));
    					}
    					cow[p->v]=1;
    				}
    
    			}
    	}
    	return true;
    }
    
    int can[MAXN];
    int main()
    {
    	freopen("cowngress.in","r",stdin);
    	freopen("cowngress.out","w",stdout);
    	scanf("%d%d",&N;,&M;);
    	for(int i=1;i<=M;i++)
    	{
    		char cva,cvb;
    		scanf("%d",a+i);
    		scanf(" %c",&cva;);
    		scanf("%d",b+i);
    		scanf(" %c",&cvb;);
    		va[i]=(cva=='Y');
    		vb[i]=(cvb=='Y');
    		addedge(a[i],va[i],i);
    		addedge(b[i],vb[i],i);
    	}
    	memset(sta,-1,sizeof(sta));
    	for(int i=1;i<=N;i++)
    	{
    		memset(sta,-1,sizeof(sta));
    		sta[i]=0;
    		if (bfs(i))
    			can[i]+=1;
    		memset(sta,-1,sizeof(sta));
    		sta[i]=1;
    		if (bfs(i))
    			can[i]+=2;
    		if (can[i]==0)
    		{
    			printf("IMPOSSIBLE\n");
    			return 0;
    		}
    	}
    	for(int i=1;i<=N;i++)
    	{
    		switch(can[i])
    		{
    			case 1:printf("N");break;
    			case 2:printf("Y");break;
    			case 3:printf("?");break;
    			default:return -1;
    		}
    	}
    	printf("\n");
    	return 0;
    }
    
    

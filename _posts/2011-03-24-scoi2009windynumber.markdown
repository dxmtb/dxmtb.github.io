---
author: admin
comments: true
date: 2011-03-24 12:34:19+00:00
layout: post
slug: scoi2009windynumber
title: SCOI2009 windy数
wordpress_id: 358
categories:
- 解题报告
tags:
- 动态规划
- 解题报告
---

dp之后减去不合法的方案就可以了。

    
    
    /*
    ID: dxmtb
    PROG: [SCOI2009]windy数 
    TIME: 2011.03.23
    STATE: Solved
    MEMO: dp
    */
    #include <cstdio>
    #include <cstdlib>
    
    const int MAXN=12;
    
    int bit[MAXN],size;
    int d[MAXN][10];
    
    int dfs(int dep)
    {
    	if (dep==0)
    		return 0;
    	int tmp=0;
    	for(int i=bit[dep-1]+1;i<=9;i++)
    		if (abs(bit[dep]-i)>=2)
    			tmp+=d[dep-1][i];
    	if (abs(bit[dep-1]-bit[dep])>=2)
    		tmp+=dfs(dep-1);
    	return tmp;
    }
    
    int getans(int n)
    {
    	if (n<=9)
    		return n;
    	size=0;
    	while(n)
    	{
    		bit[size++]=n%10;	
    		n/=10;
    	}
    	for(int i=0;i<=9;i++)
    		d[0][i]=1;
    	for(int i=1;i<size;i++)
    		for(int j=0;j<=9;j++)
    		{
    			d[i][j]=0;
    			for(int k=0;k<=j-2;k++)
    				d[i][j]+=d[i-1][k];
    			for(int k=j+2;k<=9;k++)
    				d[i][j]+=d[i-1][k];
    		}
    	int ans=0;
    	for(int i=0;i<size-1;i++)
    		for(int j=1;j<=9;j++)
    			ans+=d[i][j];
    	for(int i=1;i<=bit[size-1];i++)
    		ans+=d[size-1][i];
    	ans-=dfs(size-1);
    	return ans;
    }
    
    int A,B;
    int main()
    {
    	freopen("1026.in","r",stdin);
    	scanf("%d%d",&A;,&B;);
    	printf("%d\n",getans(B)-getans(A-1));
    	return 0;
    }
    

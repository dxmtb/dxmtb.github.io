---
author: admin
comments: true
date: 2011-12-30 10:11:13+00:00
layout: post
slug: strstrvskmp
title: 极限情况下strstr对比KMP
wordpress_id: 475
categories:
- 犄角旮旯
---

什么情况下strstr会达到最坏情况呢？考虑以下情况：

str1: 字母a重复N次 + 字母b

str2：字母a重复M次 + 字母b

粗略估计复杂度

strstr: O(N+N-1+N-2+...+N-M) = O((N-M)*M)

kmp:O(N+M)

我们就取M=N/2，N=100000。

实验结果就是KMP不到1秒，strstr没跑出来呢……

下附程序:

strstr:
<pre lang='CPP'>
#include <cstring>
#include <cstdio>
#include <ctime>

const int MAXN=1000005;

char str1[MAXN],str2[MAXN];

int main()
{
	freopen("data.in","r",stdin);
	freopen("data.out","w",stdout);
	scanf("%s",str1);
	scanf("%s",str2);
	int T=time(0);
	char *p=str1;
	while(true)
	{
		p=strstr(p,str2);
		if (p==NULL)
			break;
		printf("%d\n",p-str1);
		p++;
	}
	printf("%d\n",time(0)-T);
	return 0;
}
</pre>

KMP:
<pre lang='CPP'>
#include <cstring>
#include <cstdio>
#include <ctime>

const int MAXN=1000005;

void kmp(char *str1,char *str2)
{
	static int next[MAXN];
	int N=strlen(str1),M=strlen(str2);
	next[0]=-1;
	for(int i=1,j=-1;i<M;i++)
	{
		while(j>=0 && str2[i]!=str2[j+1])
			j=next[j];
		if (str2[i]==str2[j+1])
			j++;
		next[i]=j;
	}
	for(int i=0,j=-1;i<N;i++)
	{
		while(j>=0 && str1[i]!=str2[j+1])
			j=next[j];
		if (str1[i]==str2[j+1])
			j++;
		if (j==M-1)
		{
			printf("%d\n",i-M+1);
			j=next[j];
		}
	}
}

char str1[MAXN],str2[MAXN];

int main()
{
	freopen("data.in","r",stdin);
	freopen("data.out","w",stdout);
	scanf("%s",str1);
	scanf("%s",str2);
	int T=time(0);
	kmp(str1,str2);
	printf("%d\n",time(0)-T);
	return 0;
}
</pre>

maker:
<pre lang='CPP'>
#include <cstdio>

const int N=1000000;

int main()
{
	freopen("data.in","w",stdout);
	for(int i=0;i<N;i++)
		printf("a");
	printf("b\n");
	for(int i=0;i<N/2;i++)
		printf("a");
	printf("b\n");
	return 0;
}
</pre>
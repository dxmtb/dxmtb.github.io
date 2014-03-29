---
author: admin
comments: true
date: 2011-04-07 14:03:26+00:00
layout: post
slug: hust1016-black-white-tree
title: HUST1016 Black-White Tree
wordpress_id: 421
categories:
- 解题报告
tags:
- 动态树
- 解题报告
---

这道题是用动态树做的。每个splay只要维护最靠上的黑点就行了。

    
    
    /*
     * Problem: hust1016 Black-White Tree
     * Time: 2011.04.07
     * Author: dxmtb
     * State: Solved
     * Memo: 动态树
    */
    #include <cstdio>
    #include <queue>
    using namespace std;
    const int MAXN=1000005;
    
    struct ANode
    {
    	int v;
    	ANode *next;
    	ANode(int _v,ANode *_next):
    		v(_v),next(_next){}
    }*adj[MAXN];
    
    inline void addedge(int u,int v)
    {
    	adj[u]=new ANode(v,adj[u]);
    	adj[v]=new ANode(u,adj[v]);
    }
    
    struct Node
    {
    	int id;
    	int depth,size;
    	bool co,root;
    	Node *low;
    	Node *c[2],*father;
    	Node *rz();
    	Node *maintain();
    	void splay();
    	void zig(bool);
    	Node();
    }pool[MAXN],*null=pool;
    
    Node::Node()
    {
    	id=this-pool;
    	c[0]=c[1]=father=low=null;
    	co=false;
    }
    
    Node *Node::rz()
    {
    	size=c[0]->size+1+c[1]->size;
    	return this;
    }
    
    Node *Node::maintain()
    {
    	if (co) low=this;
    	else low=null;
    	if (c[0]->low!=null)
    		if (low==null || c[0]->low->depth<low->depth)
    			low=c[0]->low;
    	if (c[1]->low!=null)
    		if (low==null || c[1]->low->depth<low->depth)
    			low=c[1]->low;
    	rz();
    	return this;
    }
    
    void Node::zig(bool d)
    {
    	Node *p=father;
    	p->c[d]=c[!d];
    	c[!d]->father=p;
    	p->maintain();
    	if (father->father->c[0]==father) father->father->c[0]=this;
    	if (father->father->c[1]==father) father->father->c[1]=this;
    	father=p->father;
    	c[!d]=p;
    	p->father=this;
    	maintain();
    	if (p->root)
    		p->root=false,root=true;
    }
    
    void Node::splay()
    {
    	while(!root)
    	{
    		bool d=(father->c[1]==this);
    		if (father->root)
    		{
    			zig(d);
    			break;
    		}
    		bool dd=(father->father->c[1]==father);
    		if (d==dd) father->zig(dd),zig(d);
    		else zig(d),zig(dd);
    	}
    }
    
    Node *access(Node *u)
    {
    	Node *v=null;
    	while(u!=null)
    	{
    		u->splay();
    		u->c[1]->root=true;
    		u->c[1]=v;
    		v->root=false;
    		u->maintain();
    		v=u;
    		u=u->father;
    	}
    	return v;
    }
    
    void change(int u)
    {
    	Node *p=pool+u;
    	p->splay();
    	p->co^=1;
    	p->maintain();
    }
    
    void query(int u)
    {
    	Node *p=access(pool+u);
    	if (p->low==null) printf("-1\n");
    	else printf("%d\n",p->low->id);
    }
    
    queue<int> q;
    void bfs()
    {
    	q.push(1);
    	pool[1].rz();
    	while(q.size())
    	{
    		int u=q.front();
    		q.pop();
    		for(ANode *p=adj[u];p;p=p->next)
    			if (!pool[p->v].size)
    			{
    				pool[p->v].father=pool+u;
    				pool[p->v].depth=pool[u].depth+1;
    				q.push(p->v);
    				pool[p->v].rz();
    			}
    	}
    }
    
    int N,C;
    int main()
    {
    	scanf("%d%d",&N;,&C;);
    	for(int i=0;i<N-1;i++)
    	{
    		int u,v;
    		scanf("%d%d",&u;,&v;);
    		addedge(u,v);
    	}
    	for(int i=1;i<=N;i++)
    		pool[i].root=true;
    	bfs();
    	while(C--)
    	{
    		int c,v;
    		scanf("%d%d",&c;,&v;);
    		if (c==0)
    			change(v);
    		else
    			query(v);
    	}
    	return 0;
    }
    

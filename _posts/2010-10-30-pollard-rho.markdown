---
author: admin
comments: true
date: 2010-10-30 12:37:39+00:00
layout: post
slug: pollard-rho
title: 整数因数分解的Pollard-rho算法
wordpress_id: 91
categories:
- 算法珠玑
tags:
- 数论
- 算法
---

顾名思义，Pollard-rho算法的功能是分解因式。先贴代码。

    
    
    int pollard_rho(int n, int c)
    {
    	int x, y, d, i = 1, k = 2;
    	x = rand() % (n - 1) + 1;
    	y = x;
    	while (true) {
    		++i;
    		x = (modular_multi(x, x, n) + c) % n;
    		d = gcd(y - x, n);
    		if (1 < d && d < n)
    			return d;
    		if (x == y)
    			return n;
    		if (i == k)
    			y = x, k <<= 1;
    	}
    }
    


有关函数请参见[Miller-Rabbin随机性素数测试算法](http://www.dxmtb.com/blog/miller-rabbin/)

由d=gcd(y-x,n)可以看出d一定是正确的。复杂度呢？根据算法导论的有关分析，找出一个大小为p的小因子只需O(√p)的时间，是很快的。但是，不排除找不到的可能，或者如果n是一个素数，就很花时间。所以，调用前最好判断是否为素数，如果算法失败的话（即返回n），可以尝试改变c的值。

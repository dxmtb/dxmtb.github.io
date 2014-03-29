---
author: admin
comments: true
date: 2010-10-30 05:12:09+00:00
layout: post
slug: miller-rabbin
title: Miller-Rabbin随机性素数测试算法
wordpress_id: 82
categories:
- 算法珠玑
tags:
- 数论
- 算法
---

普通的素数测试我们有O(√ n)的试除算法。事实上，我们有O(slog³n)的算法。

定理一：假如p是质数，且(a,p)=1，那么a^(p-1)≡1(mod p)。即假如p是质数，且a,p互质，那么a的(p-1)次方除以p的余数恒等于1。（费马小定理）

该定理的逆命题是不一定成立的，但是令人可喜的是大多数情况是成立的。

于是我们就得到了一个定理的直接应用，对于待验证的数p，我们不断取a∈［1，p-1]且a∈Z，验证a^(p-1) mod p是否等于1，不是则p果断不是素数，共取s次。其中a^(p-1) mod p可以通过把p-1写成二进制，由(a*b)mod c=(a mod c)*b mod c，可以在t=log(p-1)的时间内计算出解，如考虑整数相乘的复杂度，则一次计算的总复杂度为log³(p-1)。这个方法叫快速幂取模。

为了提高算法的准确性，我们又有一个可以利用的定理。
定理二：对于0<x<p，x^2 mod p =1 => x=1或p-1。

我们令p-1=(2^t)*u，即p-1为u二进制表示后面跟t个0。我们先计算出x[0]=a^u mod p ，再平方t次并在每一次模p，每一次的结果记为x[i]，最后也可以计算出a^(p-1) mod p。若发现x[i]=1而x[i-1]不等于1也不等于p-1，则发现p果断不是素数。

可以证明，使用以上两个定理以后，检验s次出错的概率至多为2^(-s)，所以这个算法是很可靠的。

需要注意的是，为了防止溢出（特别大的数据），a*b mod c 也应用类似快速幂取模的方法计算。当然，数据不是很大就可以免了。

下面是我的程序。

    
    
    typedef unsigned long long LL;
    
    LL modular_multi(LL x,LL y,LL mo)
    {
    	LL t;
    	x%=mo;
    	for(t=0;y;x=(x<<1)%mo,y>>=1)
    		if (y&1)
    			t=(t+x)%mo;
    	return t;
    }
    
    LL modular_exp(LL num,LL t,LL mo)
    {
    	LL ret=1,temp=num%mo;
    	for(;t;t>>=1,temp=modular_multi(temp,temp,mo))
    		if (t&1)
    			ret=modular_multi(ret,temp,mo);
    	return ret;
    }
    
    bool miller_rabbin(LL n)
    {
    	if (n==2)return true;
    	if (n<2||!(n&1))return false;
    	int t=0;
    	LL a,x,y,u=n-1;
    	while((u&1)==0) t++,u>>=1;
    	for(int i=0;i<S;i++)
    	{
    		a=rand()%(n-1)+1;
    		x=modular_exp(a,u,n);
    		for(int j=0;j<t;j++)
    		{
    			y=modular_multi(x,x,n);
    			if (y==1&&x;!=1&&x;!=n-1)
    				return false;
    			x=y;
    		}
    		if (x!=1)
    			return false;
    	}
    	return true;
    }
    

---
author: admin
comments: true
date: 2012-02-23 07:49:29+00:00
layout: post
slug: steinspython
title: 看动漫用的python脚本
wordpress_id: 493
categories:
- 犄角旮旯
tags:
- python
- 命运石之门
---

某神牛推荐我看《命运石之门》，我本想玩原作，但原作又没有汉化，自己日语不会，于是干脆直接看吧……

不知道其他人怎么找的动漫，我下动漫的步骤就是



	
  1. 在verycd上找到相应的资源

	
  2. 在115上找到相同的资源

	
  3. 用用优蛋下载


因为verycd上资源比较全，但下载速度实在不敢恭维。而115资源索引不太好，但是下载速度很给力，而且基本google都有收录。

但是一个一个找太麻烦，这种事情应该可以交给电脑吧……于是我就想到了看过却从没有用过的python。

于是就有了以下脚本……

    
    
    from xml.dom import minidom
    import urllib2,urllib
    
    def search(word):
     url='https://www.google.com.hk/search?ix=seb&sourceid;=chrome&ie;=UTF-8&q;=' + word
     req=urllib2.Request(url)
     req.add_header("User-Agent",'Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.2; .NET CLR 1.1.4322)')
     opener=urllib2.build_opener()
     text=opener.open(req).read()
     return text
    
    file = open('hello.txt','w');
    response = urllib2.urlopen('http://www.verycd.com/topics/2888738/');
    html = response.read();
    file.write(html);
    file.close();
    
    file = open('output.txt','w');
    
    i = 0;
    for k in range(1, 23):
        if k < 10:
            s = '0' + str(k)
        else:
            s = str(k)
        s = '[' + s + ']'
        j = html.find('[SumiSora&CASO;][Steins_Gate]'+ s +
                      '[GB_Big5][x264_aac][720p]', i)
        if j == -1:
            break
        ind = html[j+58: j+66]
        i = j + 1
        spage = search(ind)
        p = spage.find('http://115.com/file/')
        if spage[p+28] != '&':
            nowurl = spage[p: p+30]
        else:
            nowurl = spage[p: p+28]
        #print nowurl
        response = urllib2.urlopen(nowurl)
        nowurl = response.read()
        pos = nowurl.find('pick_code')
        print 'http://115.com/file/' + nowurl[pos+12:pos+20]
    


这个脚本会以次把每一集《命运石之门》的115下载地址输出。因为优蛋没试出接口，貌似也不支持批量下载，只能一个个手动添了……这点很遗憾……话说python的库确实很强大。
这个脚本粘了其他人的一点代码……声明下……有问题联系我……
从开始的C++到pascal又到C++，到最近的javascript、python，我觉得语言只不过是辅助我们创造、实现思想的工具。语言到底有什么精髓呢？我至今也没能理解。


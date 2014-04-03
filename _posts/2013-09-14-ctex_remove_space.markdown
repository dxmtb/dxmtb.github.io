---
author: admin
comments: true
date: 2013-09-14 10:37:19+00:00
layout: post
slug: ctex_remove_space
title: ctex下取消数字和汉字间的空格
wordpress_id: 554
categories:
- 共同分享
tags:
- ctex
- latex
- xelatex
- 空格
---

在mac下写中文的tex用了ctex这个包，这个包有一个优点就是在汉字与数字或英文的分割处会自动加一个空格，无疑这个功能是非常好的。

但是有时候空间很紧张，结果就不想要这个空格。

一个丑陋的方法就是在间隔处加上
`
\hspace{0pt}
`
这样它们之间的空白距离就是0了~

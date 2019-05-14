---
layout: post
status: publish
published: true
title: Java 到 C# 的virtual 陷阱
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 26
wordpress_url: http://blog.byneil.com/?p=26
date: '2013-05-16 15:49:14 +0000'
date_gmt: '2013-05-16 07:49:14 +0000'
categories:
- c#
tags: []
comments: []
---
<p>java 的code到c#code的时候, &nbsp;注意java的方法默认就是virtual的, 不小心的话再c#这边就会漏掉, 而不能覆盖掉父类的方法,导致逻辑出错.</p>
<p>&nbsp;</p>

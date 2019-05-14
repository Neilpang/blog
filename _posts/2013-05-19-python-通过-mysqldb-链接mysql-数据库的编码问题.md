---
layout: post
status: publish
published: true
title: python 通过 MySQLdb 链接mysql 数据库的编码问题
author: Neil




author_login: Neil

wordpress_id: 31
wordpress_url: http://blog.byneil.com/?p=31
date: '2013-05-19 18:10:01 +0000'
date_gmt: '2013-05-19 10:10:01 +0000'
categories:
- python
tags: []
comments: []
---
<p>python 通过 MySQLdb 链接mysql</p>
<p>mysql使用的是 utf8编码，出现乱码。</p>
<p>解决方法：</p>
<pre class="brush: python; gutter: true">conn = MySQLdb.connect (host = "localhost", user = "root", passwd = "", db = "aaa", charset="utf8")

db = MySQLdb.connect(host=DB_HOST, user=DB_USER, passwd=DB_PASS, db=DB_NAME, charset="utf8", use_unicode=True)</pre>
<p>&nbsp;</p>
<p>http://stackoverflow.com/questions/9154998/python-encoding-mysql</p>

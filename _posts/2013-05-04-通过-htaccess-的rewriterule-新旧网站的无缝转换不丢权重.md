---
layout: post
status: publish
published: true
title: 通过.htaccess 的rewriterule 新旧网站的无缝转换,不丢权重
author: Neil




author_login: Neil

wordpress_id: 19
wordpress_url: http://blog.byneil.com/?p=19
date: '2013-05-04 20:51:43 +0000'
date_gmt: '2013-05-04 12:51:43 +0000'
categories:
- PHP_Mysql
tags: []
comments: []
---
<p>举个例子, &nbsp;本来有个 A.com 的网站, &nbsp;做了一定的人气, 有了权重. &nbsp;因为人流量上升, 这个时候想更换网站程序, 比如做成一个论坛社区.</p>
<p>很简单想到的就是把 A.com 直接指向新的网站就行了. &nbsp;但是这样, 以前的A.com 的所有的外链都失效了. 对权重非常不利.</p>
<p>主要目标有两个, 第一不丢失以前的外链. &nbsp;第二让A.com的权重完全转到新的论坛.</p>
<p>我们可以这样实现. &nbsp;1. 保留原网站, 这是为了确保, 外链都有效. &nbsp;2, 把 A.com &nbsp;重定向到 &nbsp;bbs.A.com</p>
<p>这里有一个问题,就是如果把A.com 重定向到了bbs.A.com了, 外链怎么保持呢. 原来的外链都是 http://a.com/abc/def.php 这种形式的.</p>
<p>这个时候想到的就是有条件的重定向, &nbsp;当用户直接访问A.com的时候, 重定向到bbs.A.con. &nbsp;如果用户是通过这种形式&nbsp;http://a.com/abc/def.php 访问的网站, &nbsp;则不重定向, 让它依然有效.</p>
<p>思路就是检测请求的uri, 如果没有uri 就直接重定向. 如果有uri 则不重定向.</p>
<p>这在php中很容易实现. 但是为了执行效率, 我决定改写.htaccess文件来实现.</p>
<p>在.htaccess 文件的最开始, 插入一下代码</p>
<pre class="brush: actionscript3; gutter: true">

RewriteCond %{REQUEST_URI} =/     #直接输入a.com访问的话，uri不是空，而是斜线：&#039;/&#039;
RewriteCond %{QUERY_STRING} ^$    #正则表达式 ^和$ 放在一起，表示空，之所以加上Querystring，是防止用户这样访问： a.com/?id=124
RewriteRule .* http://bbs.a.com [R=301,L] #重定向到新网址


</pre>
<p>搞定.</p>
<p>希望大家多多交流.</p>
<p>&nbsp;</p>

---
layout: post
status: publish
published: true
title: C# mysql 链接 遇到 异常 Authentication with old password no longer supported, use
  4.1 style passwords.
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 5
wordpress_url: http://blog.byneil.com/?p=5
date: '2013-04-30 11:38:24 +0000'
date_gmt: '2013-04-30 03:38:24 +0000'
categories:
- PHP_Mysql
tags: []
comments: []
---
<p>用最新版的 mysql connector 去链接 远程的mysql服务器.</p>
<p>遇到 异常 Authentication with old password no longer supported, use 4.1 style passwords.</p>
<p>上网搜, 官方说是服务器的密码版本新, &nbsp;客户端的版本太低, 连不上. 要更新我的客户端. &nbsp; 但是我的connector 是刚从官方下载的最新的啊. 这不科学啊.</p>
<p>还有人说是去服务器上服务器上的密码存储方式 改成旧版本,这样就可以了. &nbsp;但是服务器不是我的, &nbsp;我没有权限去改服务器的配置.</p>
<p>&nbsp;</p>
<p>最后发现 一个帖子<a href="http://stackoverflow.com/questions/13706463/authentication-method-mysql-old-password-not-supported">http://stackoverflow.com/questions/13706463/authentication-method-mysql-old-password-not-supported</a></p>
<p>里面说&nbsp;MysqL Connector/NET 6.6.x (as of 6.6.2) dropped support for old password style authentication (it was deprecated due to being insecure and there were documented ways to attack it).</p>
<p>其实就是说 .net的connector 已经抛弃了旧的密码方式, 只支持新的密码方式. &nbsp; 突然明白了. &nbsp; 应该是客户端的密码认证方式太新, 而服务器的密码是旧的方式存储的.</p>
<p>所以就要去把服务器的密码存储方式改成新的. &nbsp;怎么改呢. 找到这个地址&nbsp;&nbsp;<a href="http://writecodepeople.blogspot.com/2013/03/mysql-41-style-password.html">http://writecodepeople.blogspot.com/2013/03/mysql-41-style-password.html</a></p>
<p>在服务器端只提供了phpmyadmin, 没有console啊. &nbsp; 我记得有个地方可以执行sql语句</p>
<p>于是去服务器上打开phpmyadmin. &nbsp;打开数据库. &nbsp;打开Sql tab, 这里可以执行 sql语句.哈哈</p>
<p><img alt="" src="http://images.cnitblog.com/blog/38373/201304/22215455-1827a43a2c584a8bbde03ce257f584de.png" /></p>
<p>执行这两句:</p>
<p>SET old_passwords=FALSE;</p>
<p>SET PASSWORD = PASSWORD('重设密码');</p>
<p>之后, 在用c# connector 去连. &nbsp;成功.</p>
<p>reffer: <a title="byNeil" href="http://byNeil.com" target="_blank">http://byNeil.com</a></p>

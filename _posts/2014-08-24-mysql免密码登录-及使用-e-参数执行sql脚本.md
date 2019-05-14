---
layout: post
status: publish
published: true
title: mysql免密码登录, 及使用-e 参数执行sql脚本
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 723
wordpress_url: https://blog.byneil.com/?p=723
date: '2014-08-24 01:02:56 +0000'
date_gmt: '2014-08-23 17:02:56 +0000'
categories:
- PHP_Mysql
tags: []
comments: []
---
<p>我们在编写shell脚本的时候,经常需要和 mysql 交互.</p>
<p>如果是交互环境,可能使用这样的命令登录到mysql</p>
<pre><code>#mysql &ndash;uroot &ndash;p
</code></pre>
<p>然后按提示输入密码, 登录. 如果在脚本中, 我们就不得不把密码写在 &ndash;p 参数后面. 这样 很容易暴露密码.</p>
<p>幸好mysql提供的有解决方案.  在 &ldquo;~/.my.cnf&rdquo; 文件中保存密码就行了.</p>
<p>MySQL官方文档</p>
<p>文件内容大概如下:</p>
<pre><code>[client]

password="MySQL密码"

user=MySQL用户名
</code></pre>
<p>其中user 行可以省略, 默认使用当前的用户名填充mysql的登录用户名</p>
<p>再次使用 mysql 命令的时候,就无需输入用户名和密码了,可以自动登录.</p>
<p>还可以给 mysql 命令使用 <code>--defaults-file</code> 参数来指定特定的配置文件路径:</p>
<pre><code>mysql --defaults-file=/folder1/folder2/filename -u 用户名

</code></pre>
<p>实现了免密码登录之后, 在脚本中就可以直接使用 -e 参数来执行sql脚本了, 而不用像交互式一样登录到mysql之后执行了.</p>
<pre><code>mysql -e "CREATE DATABASE test" 
</code></pre>
<p>到这里, 基本上就可以实现完全的无值守 mysql脚本操作了.</p>
<p>参考: http://yzs.me/2142.html</p>

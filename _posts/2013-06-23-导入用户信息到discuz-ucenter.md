---
layout: post
status: publish
published: true
title: 导入用户信息到discuz ucenter.
author: Neil




author_login: Neil

wordpress_id: 40
wordpress_url: http://blog.byneil.com/?p=40
date: '2013-06-23 18:40:29 +0000'
date_gmt: '2013-06-23 10:40:29 +0000'
categories:
- PHP_Mysql
tags: []
comments: []
---
<p>上一篇帖子:&nbsp;<a title="http://blog.byneil.com/%E7%9B%B4%E6%8E%A5%E5%AF%BC%E5%85%A5%E5%B8%96%E5%AD%90%E5%88%B0discuz-%E8%AE%BA%E5%9D%9B%E6%95%B0%E6%8D%AE%E5%BA%93/" href="http://blog.byneil.com/%E7%9B%B4%E6%8E%A5%E5%AF%BC%E5%85%A5%E5%B8%96%E5%AD%90%E5%88%B0discuz-%E8%AE%BA%E5%9D%9B%E6%95%B0%E6%8D%AE%E5%BA%93/" target="_blank">直接导入帖子到Discuz 论坛数据库</a>. 结束时说要写一篇导入用户的帖子, 一直没时间, 但是咱不能做太监,不是? 所以今天赶快补上. 在做discuz整合或者迁移是, 很多人可能遇到相同的问题, 就是用户数据怎么导入到discuz中.</p>
<p>discuz 的用户数据其实是存在 ucenter中的. ucenter是什么? 自己百度去. 简单的说, ucenter 就是discuz各个产品之间共享数据的媒介. 所以我们只需要导入到ucenter的表中就可以了.</p>
<p>同样通过上一篇文章中提到的比较方法, &nbsp;我们发现用户数据时存在&nbsp;pre_ucenter_members 这一张表中的. &nbsp;欢迎大家交流心得, 访问我的独立博客 <a title="http://byNeil.com" href="http://byNeil.com" target="_blank">http://byNeil.com</a>&nbsp;&nbsp;.下面解释一下这个表的列的含义:</p>
<p>1.&nbsp;username: &nbsp;用户名, 就是用户登录输的用户名.</p>
<p>2.&nbsp;password: 密码, 这个当然不是明文的密码, 至于怎么生成的, 后面再说. password hash =&nbsp;Md5(Md5(password) + salt);</p>
<p>3.&nbsp;email: 就是用户的email, 明文</p>
<p>4.&nbsp;regdate: 是一个int值, linux的时间戳,表示用户的注册时间.</p>
<p>5.&nbsp;salt: 盐. &nbsp;这个比较有意思, 是为了增加用户密码的安全性的. &nbsp;这个salt是一个 6位长的字符串, 它本身是注册时随机产生的. &nbsp;它的作用就是用来混在密码一起产生密码的hash值的. &nbsp;password hash =&nbsp;Md5(Md5(password) + salt);</p>
<p>&nbsp;</p>
<p>有了这几列的意思, 导入就简单多了. &nbsp;如果你知道原来用户的密码(不太可能, 除非是国内某著名网站明文存密码), &nbsp;或者知道用户密码的 MD5值, &nbsp;就可以用自己生成的salt来 为用户导入密码了. &nbsp;这样用户就能用原来的密码登陆新网站了. &nbsp; 如果不知道, 那只有重置所有用户的密码.</p>
<p>具体code就不写了, 各个语言不一样, 自己琢磨.</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>

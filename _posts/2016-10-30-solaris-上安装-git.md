---
layout: post
status: publish
published: true
title: solaris 上安装 git
author: Neil




author_login: Neil

wordpress_id: 1138
wordpress_url: https://blog.byneil.com/?p=1138
date: '2016-10-30 22:13:39 +0000'
date_gmt: '2016-10-30 14:13:39 +0000'
categories:
- 系统编程
tags: []
comments: []
---
<p>今天要在solaris上安装git。<br />
记录一下步骤：</p>
<pre><code>pkgadd -d http://get.opencsw.org/now
/opt/csw/bin/pkgutil -U
/opt/csw/bin/pkgutil -y -i git 
/usr/sbin/pkgchk -L CSWgit # list files

</code></pre>
<p>https://www.opencsw.org/package/git/</p>

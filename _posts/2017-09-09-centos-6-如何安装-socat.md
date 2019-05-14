---
layout: post
status: publish
published: true
title: centos 6 如何安装 socat
author: Neil




author_login: Neil

wordpress_id: 1238
wordpress_url: https://blog.byneil.com/?p=1238
date: '2017-09-09 13:31:09 +0000'
date_gmt: '2017-09-09 05:31:09 +0000'
categories:
- linux
tags: []
comments: []
---
<p>由于socat 并没有在 centos 6 的默认源中. 无法直接安装.</p>
<p>有两个方法可以安装.</p>
<p>第一:  可以自己下载并安装这两个软件包:</p>
<ol>
<li>https://centos.pkgs.org/6/centos-x86_64/compat-readline5-5.2-17.1.el6.x86_64.rpm.html</li>
<li>https://centos.pkgs.org/6/epel-x86_64/socat-1.7.2.3-1.el6.x86_64.rpm.html</li>
</ol>
<p>第二: 可以添加源,然后yum 安装.</p>
<pre class="line-numbers prism-highlight" data-start="1"><code class="language-null">curl http://www.convirture.com/repos/definitions/rhel/6.x/convirt.repo > /etc/yum.repos.d/convirt.repo

yum update
yum install socat
</code></pre>

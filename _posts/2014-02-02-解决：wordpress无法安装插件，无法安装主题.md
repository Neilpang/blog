---
layout: post
status: publish
published: true
title: 解决：wordpress无法安装插件，无法安装主题
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 254
wordpress_url: http://blog.byneil.com/?p=254
date: '2014-02-02 14:05:23 +0000'
date_gmt: '2014-02-02 06:05:23 +0000'
categories:
- PHP_Mysql
- 服务器建站
tags: []
comments: []
---
<p>为了提高本博客的访问速度， 前几天更换了vps， 于是昨天把博客搬家到新的vps上了。</p>
<p>一切看起来还不错。登录后台时发现后台清净了很多。</p>
<p>&#160;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image3.png"><img title="image" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="image" src="http://blog.byneil.com/wp-content/uploads/2014/02/image3_thumb.png" width="861" height="455" /></a> </p>
<p>也没注意。</p>
<p>今天尝试安装另外一个插件时除了问题。 后台搜索插件：</p>
<p>wordpress无法安装插件，无法安装主题</p>
<p>&#160;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image.png"><img title="image" style="border-left-width: 0px; border-right-width: 0px; border-bottom-width: 0px; display: inline; border-top-width: 0px" border="0" alt="image" src="http://blog.byneil.com/wp-content/uploads/2014/02/image_thumb.png" width="890" height="251" /></a> </p>
<p>第一反应是，文件夹权限不对。</p>
<p>于是， 后台去 chown, chgrp</p>
<p>&#160;</p>
<p>一对权限加到最大。</p>
<p>&#160;</p>
<p>最后发现，新站点php运行在安全模式下， 想到可能是安全模式权限不够， 于是关闭安全模式，问题解决了。</p>
<p>&#160;</p>
<p>这个插件可在后台直接更改 safemode。</p>
<p>不用去修改php配置。</p>
<p>&#160;</p>
<p><a title="http://wordpress.org/plugins/safe-mode/" href="http://wordpress.org/plugins/safe-mode/">http://wordpress.org/plugins/safe-mode/</a></p>

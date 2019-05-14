---
layout: post
status: publish
published: true
title: 添加Access-Control-Allow-Origin主机头, 授权资源跨站访问
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 1002
wordpress_url: https://blog.byneil.com/?p=1002
date: '2014-09-24 22:02:48 +0000'
date_gmt: '2014-09-24 14:02:48 +0000'
categories:
- 服务器建站
tags: []
comments: []
---
<p><em>Access-Control-Allow-Origin</em> 是html5 添加的新功能, chrome貌似前几天更新之后支持了这一特性.</p>
<p>基本上, 这是一个http的header,  用在返回资源的时候,  指定这个资源可以被哪些网域跨站访问.</p>
<p>比方说, 你的图片都放在 <em>res.byneil.com</em> 这个域下, 如果在返回的头中没有设置 <em>Access-Control-Allow-Origin</em>, 那么别的域是不能外链你的图片的.</p>
<p>当然这要取决于浏览器的实现是否遵守规范.  因为chrome最近的升级开始检查这个头了, 所以导致一些网站资源加载不进来.</p>
<p>解决方法就是 在资源的头中 加入 <em>Access-Control-Allow-Origin</em> 指定你授权的域. 我这里无所谓,就指定星号 * , 任何域都可以访问我的资源.</p>
<pre><code>Access-Control-Allow-Origin: *
</code></pre>
<p>具体操作方法, 就是在nginx的conf文件中加入以下内容:</p>
<pre><code>location / {
  add_header Access-Control-Allow-Origin *;
}
</code></pre>
<p>这样就好了.</p>

---
layout: post
status: publish
published: true
title: pfsense 可以直接安装 freebsd 的软件包
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 1234
wordpress_url: https://blog.byneil.com/?p=1234
date: '2017-09-02 22:21:13 +0000'
date_gmt: '2017-09-02 14:21:13 +0000'
categories:
- 系统编程
tags: []
comments: []
---
<p>pfsense 可以手动安装 freebsd 的软件包. 只需要三步.<br />
1. 在这个页面搜索并复制软件包的链接: https://pkg.freebsd.org/freebsd:10:x86:64/latest/All/<br />
2. 执行 <code>pkg add</code>:</p>
<pre class="line-numbers prism-highlight" data-start="1"><code class="language-null">pkg add https://pkg.freebsd.org/freebsd:10:x86:64/latest/All/socat-1.7.3.2_2.txz
</code></pre>
<ol>
<li>执行 <code>rehash</code></li>
</ol>
<pre class="line-numbers prism-highlight" data-start="1"><code class="language-null">rehash
</code></pre>
<p>参考资料:<br />
1. https://forum.pfsense.org/index.php?topic=46116.0<br />
2. https://doc.pfsense.org/index.php/Installing_FreeBSD_Packages</p>

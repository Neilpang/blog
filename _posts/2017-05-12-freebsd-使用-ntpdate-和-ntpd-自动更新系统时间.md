---
layout: post
status: publish
published: true
title: freebsd 使用 Ntpdate 和 Ntpd 自动更新系统时间
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 1186
wordpress_url: https://blog.byneil.com/?p=1186
date: '2017-05-12 20:45:57 +0000'
date_gmt: '2017-05-12 12:45:57 +0000'
categories:
- 其他分类
tags: []
comments: []
---
<p>由于某些原因,系统时间有时候不对了.<br />
freebsd 如何同步时间.</p>
<ol>
<li>手动同步:</li>
</ol>
<pre><code class="sh">ntpdate -b pool.ntp.org
</code></pre>
<p>几秒钟后, 时间就同步了.</p>
<ol>
<li>启动系统 Ntpd 服务, 自动同步:<br />
编辑系统的文件: <code>/etc/ntpd.conf</code></li>
</ol>
<pre><code>server pool.ntp.org
driftfile /etc/ntp.drift
logfile /var/log/ntpd.log
</code></pre>
<p>然后编辑: <code>/etc/rc.conf</code><br />
加入一行:</p>
<pre><code>ntpd_enable="YES"
ntpdate_enable="YES"
</code></pre>
<p>启动ntpd 服务:</p>
<pre><code>service ntpd start
</code></pre>
<p>参考:<br />
1. http://www.surlyjake.com/blog/2008/11/17/freebsd-time-updates-with-ntpdate-and-ntpd/<br />
1. https://www.cyberciti.biz/tips/freebsd-timeclock-synchronization-with-ntp-server.html</p>

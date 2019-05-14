---
layout: post
status: publish
published: true
title: '解决：fatal error C1902: Program database manager mismatch; please check your
  installation'
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 1009
wordpress_url: https://blog.byneil.com/?p=1009
date: '2014-10-13 11:57:52 +0000'
date_gmt: '2014-10-13 03:57:52 +0000'
categories:
- Windows
tags: []
comments: []
---
<p>重装系统后，VS2008编译出现错误。</p>
<pre><code>fatal error C1902: Program database manager mismatch; please check your installation
</code></pre>
<p>可能是多版本共存引起的问题。</p>
<p>找到这篇文章：http://wordrump.com/how-to-fix-the-error-fatal-error-c1902-program-database-manager-mismatch-please-check-your-installation/</p>
<p>简单暴力， copy 这三个文件：</p>
<pre><code>mspdb80.dll
mspdbcore.dll
mspdbsrv.exe
</code></pre>
<p>从<br />
<code>C:\Program Files (x86)\Microsoft Visual Studio 9.0\Common7\IDE</code><br />
到<br />
<code>c:\windows</code></p>
<p>解决。</p>

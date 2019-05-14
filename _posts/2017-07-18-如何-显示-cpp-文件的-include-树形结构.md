---
layout: post
status: publish
published: true
title: 如何 显示 cpp 文件的 include 树形结构
author: Neil




author_login: Neil

wordpress_id: 1220
wordpress_url: https://blog.byneil.com/?p=1220
date: '2017-07-18 20:21:56 +0000'
date_gmt: '2017-07-18 12:21:56 +0000'
categories:
- 系统编程
tags: []
comments: []
---
<p>在排除一下 cpp 编译错误时, 可能需要搞清楚文件包含关系.<br />
linux:</p>
<pre class="line-numbers prism-highlight" data-start="1"><code class="language-sh">gcc  -M showtime.c  my.cpp

</code></pre>
<p>windows:</p>
<pre class="line-numbers prism-highlight" data-start="1"><code class="language-sh">cl.exe  /showIncludes  my.cpp
</code></pre>
<p>参考:</p>
<ol>
<li>https://stackoverflow.com/questions/5834778/how-to-tell-where-a-header-file-is-included-from</li>
<li>https://stackoverflow.com/questions/1137966/displaying-the-include-hierarchy-for-a-c-file-in-visual-studio</li>
</ol>

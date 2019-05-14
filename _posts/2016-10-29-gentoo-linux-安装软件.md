---
layout: post
status: publish
published: true
title: gentoo linux 安装软件
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 1136
wordpress_url: https://blog.byneil.com/?p=1136
date: '2016-10-29 21:45:13 +0000'
date_gmt: '2016-10-29 13:45:13 +0000'
categories:
- linux
tags: []
comments: []
---
<p>gentoo linux 使用 <code>emerge</code> 安装 软件。</p>
<p>更新软件包信息：</p>
<pre><code>emerge  --sync
</code></pre>
<p>搜索软件包：</p>
<pre><code>emerge  --search   netcat
</code></pre>
<p>安装软件：</p>
<pre><code>emerge   curl
</code></pre>
<p>如果安装软件遇到如下错误：</p>
<pre><code># emerge    net-analyzer/openbsd-netcat

 * IMPORTANT: 11 news items need reading for repository 'gentoo'.
 * Use eselect news read to view new items.

Calculating dependencies... done!
[ebuild   R   ~] net-analyzer/openbsd-netcat-1.105-r1 

The following keyword changes are necessary to proceed:
 (see "package.accept_keywords" in the portage(5) man page for more details)
# required by net-analyzer/openbsd-netcat (argument)
=net-analyzer/openbsd-netcat-1.105-r1 ~amd64

Use --autounmask-write to write changes to config files (honoring
CONFIG_PROTECT). Carefully examine the list of proposed changes,
paying special attention to mask or keyword changes that may expose
experimental or unstable packages.

</code></pre>
<p>尝试这样：</p>
<pre><code> ACCEPT_KEYWORDS="~amd64"   emerge  net-analyzer/openbsd-netcat
</code></pre>

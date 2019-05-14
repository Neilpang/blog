---
layout: post
status: publish
published: true
title: php 操作数组 add_assoc_string 导致 崩溃
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 1023
wordpress_url: https://blog.byneil.com/?p=1023
date: '2014-11-11 17:57:11 +0000'
date_gmt: '2014-11-11 09:57:11 +0000'
categories:
- PHP_Mysql
tags: []
comments: []
---
<p>使用php内核的方法 操作数组时 导致 php崩溃。</p>
<pre><code>int add_assoc_string ( zval* $arg, char* $key, char* $str, int $duplicate )*
</code></pre>
<p><a href="http://php.undmedlibrary.org/manual/en/zend-api.add-assoc-string.php" target="_blank">http://php.undmedlibrary.org/manual/en/zend-api.add-assoc-string.php</a></p>
<p>原因是：第三个参数， 添加的字符串不能使NULL。</p>
<p>添加之前要先判。 如果是NULL， 应该使用专门加NULL的api：</p>
<pre><code>int add_assoc_null ( zval* $arg, char* $key )
</code></pre>
<p><a href="http://php.undmedlibrary.org/manual/en/zend-api.add-assoc-null.php" target="_blank">http://php.undmedlibrary.org/manual/en/zend-api.add-assoc-null.php</a></p>

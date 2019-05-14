---
layout: post
status: publish
published: true
title: Thinkphp独立分组模式下，每个分组的单独配置文件config.php位置在哪里
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 176
wordpress_url: http://blog.byneil.com/?p=176
date: '2014-01-01 00:13:12 +0000'
date_gmt: '2013-12-31 16:13:12 +0000'
categories:
- PHP_Mysql
tags: []
comments: []
---
<p>我使用的是Thinkphp 3.1.3， &nbsp;也就是今天的3.1最新版本。 其实官方3.2已经正式发布了， 看了一下，改变比较多，而且不兼容，所以暂时先不升级了。</p>
<p>我配置了三个独立分组：</p>
<pre class="brush: actionscript3; gutter: true">&#039;APP_GROUP_LIST&#039;=>&#039;Home,Admin,SN&#039;,
 &#039;DEFAULT_GROUP&#039;=>&#039;Home&#039;,
 &#039;APP_GROUP_MODE&#039;=>1,</pre>
<p>我需要对其中一个分组使用单独的config.php</p>
<p>按照官方的文档：<a href="http://doc.thinkphp.cn/manual/group_config.html">http://doc.thinkphp.cn/manual/group_config.html</a></p>
<p>--------------------------------------------------------------------------------------------------------------------</p>
<pre class="brush: actionscript3; gutter: true"></pre>
<pre class="brush: actionscript3; gutter: true">如果启用了模块分组，则可以在对每个分组单独定义配置文件，分组配置文件位于：
项目配置目录/分组名称/config.php
可以通过如下配置启用分组：</pre>
<ol>
<li>'APP_GROUP_LIST'&nbsp;=>&nbsp;'Home,Admin',&nbsp;//项目分组设定</li>
<li>&nbsp;'DEFAULT_GROUP'&nbsp;&nbsp;=>&nbsp;'Home',&nbsp;//默认分组</li>
</ol>
<pre class="brush: actionscript3; gutter: true">现在定义了Home和Admin两个分组，则我们可以定义分组配置文件如下：
Conf/Home/config.php
Conf/Admin/config.php
每个分组的配置文件仅在当前分组有效，分组配置的定义格式和项目配置是一样的。</pre>
<p>&nbsp;</p>
<p>-----------------------------------------------------------------------------------------------------------------</p>
<p>但是，不知道为什么就是不起作用，这问题困扰了我很久。 网上找到的所有答案都是从官方文档拷的，所以也一样。幸好Thinkphp是开源的。没办法，咱只有读代码了。</p>
<p>在Thinkphp/LIb/Core/Dispatcher.class.php 中找到&nbsp;dispatch() 函数， 再找到大概 163 行，有个注释：</p>
<pre class="brush: php; gutter: true">// 加载分组配置文件
 if(is_file($config_path.&#039;config.php&#039;))
&nbsp; &nbsp; &nbsp;C(include $config_path.&#039;config.php&#039;);</pre>
<p>这里就是在加载分组的独立配置文件了。</p>
<p>再倒回去10行左右， 找到 "$config_path" 的定义：</p>
<p>// 定义项目基础加载路径<br />
define('BASE_LIB_PATH', (defined('GROUP_NAME') &amp;&amp; C('APP_GROUP_MODE')==1) ? APP_PATH.C('APP_GROUP_PATH').'/'.GROUP_NAME.'/' : LIB_PATH);</p>
<p>结合这两个定义，可以知道，官方的文档说的是错的。 &nbsp;独立分组的配置文件正确的位置在：（以Admin分组为例）</p>
<pre class="brush: actionscript3; gutter: true">Admin/Config/config.php</pre>
<p>这个Admin就是你的分组目录， 不是在Config目录下新建的Admin目录。 全路径看起来应该是这样的：</p>
<pre class="brush: actionscript3; gutter: true">/app/Modules/Admin/Config/config.php</pre>
<p>打完收工。</p>

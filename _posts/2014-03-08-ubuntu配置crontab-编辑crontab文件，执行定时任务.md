---
layout: post
status: publish
published: true
title: ubuntu配置crontab, 编辑crontab文件，执行定时任务
author: Neil




author_login: Neil

wordpress_id: 450
wordpress_url: https://blog.byneil.com/?p=450
date: '2014-03-08 18:50:22 +0000'
date_gmt: '2014-03-08 10:50:22 +0000'
categories:
- linux
tags:
- cron
- crontab
comments: []
---
<p>&nbsp;</p>
<p>1. 创建或编辑用户的配置文件：</p>
<div class="csharpcode">
<pre class="alt">$ crontab -e [username]</pre>
</div>
<p>&nbsp;</p>
<p>用户的配置文件在:</p>
<div class="csharpcode">
<pre class="alt">/var/spool/cron/crontabs</pre>
</div>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/03/image15.png"><img style="display: inline; border: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb15.png" alt="image" width="841" height="38" border="0" /></a></p>
<p>每个用户都有自己的文件。我这里只有一个root</p>
<p>&nbsp;</p>
<p>2. 在里面添加行， 每行一个任务。</p>
<p>具体行内语法请参考：<a title="http://docs.oracle.com/cd/E24847_01/html/819-6951/sysrescron-1.html#sysrescron-62861" href="http://docs.oracle.com/cd/E24847_01/html/819-6951/sysrescron-1.html#sysrescron-62861">http://docs.oracle.com/cd/E24847_01/html/819-6951/sysrescron-1.html#sysrescron-62861</a></p>
<p>&nbsp;</p>
<p>修改完之后，重新加载cron配置：</p>
<div class="csharpcode">
<pre class="alt">service cron reload</pre>
</div>
<p>&nbsp;</p>
<p>3. 查看当前用户的任务列表：</p>
<div class="csharpcode">
<pre class="alt crayon-selected">crontab -l</pre>
</div>

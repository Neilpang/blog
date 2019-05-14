---
layout: post
status: publish
published: true
title: '密码太长导致mysql：ERROR] Slave I/O: error connecting to master ''replication@******:3306''
  - retry-time: 60  retries: 2, Error_code: 1045'
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 412
wordpress_url: https://blog.byneil.com/?p=412
date: '2014-03-08 15:02:04 +0000'
date_gmt: '2014-03-08 07:02:04 +0000'
categories:
- PHP_Mysql
- 服务器建站
tags:
- mysql
- 数据备份
comments: []
---
<p>配置mysql主从备份，遇到在从服务器上slave启动不成功。</p>
<p>查看错误日志，</p>
<p>ERROR] Slave I/O: error connecting to master <a href="mailto:'replication@*****:3306'">'replication@*****:3306'</a> - retry-time: 60&nbsp; retries: 2, Error_code: 1045</p>
<p>&nbsp;</p>
<p>第一想法是密码错误了。&nbsp; 于是先停止slave，重设master。</p>
<pre class="lang:default decode:true " >stop slave;

CHANGE MASTER TO MASTER_HOST='108.***.***.***',
MASTER_USER='repl_user2',
MASTER_PASSWORD='hfn98kMnsdfposdfasdfsadfsdjntuyuzcndfasdfsfsdfdsfsdfsdfsdf',

MASTER_LOG_FILE='mysql-bin.000008',
MASTER_LOG_POS=107;</pre>
<p>然后重启 slave：</p>
<div class="csharpcode">
<pre class="alt">start slave;</pre>
</div>
<p>&nbsp;</p>
<p>之后发现还是：</p>
<p>Slave_IO_Running: NO</p>
<p>&nbsp;</p>
<p>网上搜了很多， 说这个1045错误代码就是密码不对。如此重试了几次，确保密码复制粘贴没错。</p>
<p>手动用远程登录也没有问题，证明用户名和密码都没错。</p>
<p>&nbsp;</p>
<p>查看日志，也没有详细信息。</p>
<p>在mysql的data目录下游荡，希望有什么有用的信息：</p>
<p>&nbsp;</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image11.png"><img style="display: inline; border: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb11.png" alt="image" width="934" height="137" border="0" /></a></p>
<p>&nbsp;</p>
<p>我们知道 master.info 记录的刚才设置的master信息的。 实在没有别的思路了，看看master.info 吧。</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image12.png"><img style="display: inline; border: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb12.png" alt="image" width="776" height="273" border="0" /></a></p>
<p>&nbsp;</p>
<p>可以看到，里面记录了刚才设的master信息。</p>
<p>&nbsp;</p>
<p>一眼看过去觉得有点不对。</p>
<p>哪里不对？&nbsp; 发现这里记录的密码长度变短了。</p>
<p>&nbsp;</p>
<p>我再前面change master语句中给出的密码是很长的：</p>
<p>hfn98kMnsdfposdfasdfsadfsdjntuyuzcndfasdfsfsdfdsfsdfsdfsdf</p>
<p>但是这里记录的密码只有前面一段：</p>
<p>hfn98kMnsdfposdfasdfsadfsdjntuyuzcn</p>
<p>&nbsp;</p>
<p>可以发现， 密码被截断了。</p>
<p>&nbsp;</p>
<p>我知道了。应该是问题在这。于是去主服务器把密码改短。 再回来试试。</p>
<p>果然就成功了。</p>
<p>&nbsp;</p>
<p>不知道为什么会被截断，但是希望能帮到你。 有知道的希望指点一下。</p>
<p>blog.byneil.com</p>

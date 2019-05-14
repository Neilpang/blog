---
layout: post
status: publish
published: true
title: useradd,groupadd 添加用户和用户组
author: Neil




author_login: Neil

wordpress_id: 390
wordpress_url: https://blog.byneil.com/?p=390
date: '2014-03-06 00:05:37 +0000'
date_gmt: '2014-03-05 16:05:37 +0000'
categories:
- linux
tags:
- ubuntu
- useradd
- groupadd
- id
- 添加用户
- iptables
comments: []
---
<ul>
<li><code>id</code> 命令可以查看当前用户的详细信息:</li>
</ul>
<pre><code>root@vm:~# id
uid=0(root) gid=0(root) groups=0(root)
</code></pre>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image6.png"><img title="image" style="border-left-width: 0px; border-right-width: 0px; border-bottom-width: 0px; display: inline; border-top-width: 0px" border="0" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb6.png" width="738" height="51"></a> </p>
<ul>
<li>在 <code>/etc/group</code> 文件中记录着所有的用户组：</li>
</ul>
<pre><code>root@vm:~# cat /etc/group
</code></pre>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image7.png"><img title="image" style="border-left-width: 0px; border-right-width: 0px; border-bottom-width: 0px; display: inline; border-top-width: 0px" border="0" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb7.png" width="664" height="288"></a></p>
<ul>
<li>在 <code>/etc/passwd</code> 文件中记录了所有的用户的信息</li>
</ul>
<pre><code>root@vm:~# cat /etc/passwd
</code></pre>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image31.png"><img title="image" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image3_thumb.png" width="787" height="333"></a> </p>
<p>添加用户组：</p>
<pre><code>root@vm:~# groupadd backupuser
</code></pre>
<p>禁止用户组访问网络：</p>
<pre><code>iptables -A OUTPUT -m owner --gid-owner backupuser -p all  -j DROP
</code></pre>
<p>保存 iptables设置:</p>
<pre><code>iptables-save  > ip.conf
</code></pre>
<p>恢复iptables设置：</p>
<pre><code>iptables-restore  < ip.conf
</code></pre>
<p>需要写入开机脚本，确保每次重启生效。</p>
<pre><code>/etc/rc.local
</code></pre>
<p>添加用户，指定组， &ndash;m 自动生成home文件夹</p>
<pre><code>useradd user1 -g backupuser -m
</code></pre>
<p>未完, 待续&hellip;&hellip;.</p>

---
layout: post
status: publish
published: true
title: Ubuntu 启用磁盘配额 quota
author: Neil




author_login: Neil

wordpress_id: 843
wordpress_url: https://blog.byneil.com/?p=843
date: '2014-08-31 18:15:45 +0000'
date_gmt: '2014-08-31 10:15:45 +0000'
categories:
- linux
tags: []
comments: []
---
<p><code>quota</code>  可以为用户或用户组设置磁盘配额. 限制用户或用户组能使用的磁盘大小.</p>
<h1>1. 安装 quota :</h1>
<pre><code>sudo apt-get install quota
</code></pre>
<hr />
<h1>2. 编辑 <code>/etc/fstab</code> 文件,启用 quota</h1>
<pre><code>vi  /etc/fstab
</code></pre>
<p>添加 <code>usrjquota=aquota.user,grpjquota=aquota.group,jqfmt=vfsv0</code> 到需要启用 quota 的分区. 比如 <code>/</code>:</p>
<pre><code># /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda2 during installation
UUID=7cf8dc2b-2541-4684-8931-844b6bd01e9c /               ext4    errors=remount-ro,usrjquota=aquota.user,grpjquota=aquota.group,jqfmt=vfsv0 0       1
# /boot was on /dev/sda1 during installation
UUID=4541b27d-84d6-4f70-9077-7cb9308634b6 /boot           ext4    defaults        0       2
# swap was on /dev/sda3 during installation
UUID=ccae84a6-7423-4e60-9448-0cf05a053fbc none            swap    sw              0       0
</code></pre>
<hr />
<h1>3. 重新挂载分区:</h1>
<pre><code>touch /aquota.user /aquota.group
chmod 600 /aquota.*
mount -o remount /
</code></pre>
<hr />
<h1>4. 启用 quota :</h1>
<pre><code>quotacheck -avugmc
quotaon -avug
</code></pre>
<hr />
<p>参考:</p>
<blockquote>
<p><a href="http://www.howtoforge.com/how-to-set-up-journaled-quota-on-debian-lenny">http://www.howtoforge.com/how-to-set-up-journaled-quota-on-debian-lenny</a><br />
  <a href="http://manpages.ubuntu.com/manpages/hardy/zh_CN/man1/quota.1.html">http://manpages.ubuntu.com/manpages/hardy/zh_CN/man1/quota.1.html</a></p>
</blockquote>

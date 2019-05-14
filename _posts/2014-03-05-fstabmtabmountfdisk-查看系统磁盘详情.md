---
layout: post
status: publish
published: true
title: fstab,mtab,mount,fdisk 查看系统磁盘详情
author: Neil




author_login: Neil

wordpress_id: 379
wordpress_url: https://blog.byneil.com/?p=379
date: '2014-03-05 22:08:40 +0000'
date_gmt: '2014-03-05 14:08:40 +0000'
categories:
- linux
tags:
- linux
- df
- fdisk
- mount
- fstab
- mtab
comments: []
---
<p>&#160;</p>
<p>fstab 文件想必大家都很熟悉，记录了计算机上硬盘分区的相关信息，启动 Linux 的时候，检查分区的 fsck 命令，和挂载分区的 mount 命令，都需要 fstab 中的信息，来正确的检查和挂载硬盘。</p>
<p>&#160;</p>
<p>root@t1:/home# cat /etc/fstab   <br />proc&#160; /proc&#160;&#160;&#160;&#160;&#160;&#160; proc&#160;&#160;&#160; defaults&#160;&#160;&#160; 0&#160;&#160;&#160; 0    <br />none&#160; /dev/pts&#160;&#160;&#160; devpts&#160; rw,gid=5,mode=620&#160;&#160;&#160; 0&#160;&#160;&#160; 0    <br />none&#160; /run/shm&#160;&#160;&#160; tmpfs&#160;&#160; defaults&#160;&#160;&#160; 0&#160;&#160;&#160; 0</p>
<p>&#160;</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image1.png"><img title="image" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb1.png" width="617" height="80" /></a> </p>
<p>除了 fstab 文件之外，还有一个 mtab 文件，和 fstab 文件一样在 /etc 文件下，位于 /etc/mtab。</p>
<p>mtab 文件记录的是，当前已挂载的分区信息。每当 mount 挂载分区、umount 卸载分区，都会动态更新 mtab，mtab 总是保持着当前系统中已挂载的分区信息，fdisk、df 这类程序，必须要读取 mtab 文件，才能获得当前系统中的分区挂载情况。 原文来自 blog.byneil.com</p>
<p>&#160;</p>
<p>root@t1:/home# cat /etc/mtab    <br />/dev/simfs / simfs rw,relatime,usrquota,grpquota 0 0    <br />proc /proc proc rw,relatime 0 0    <br />sysfs /sys sysfs rw,relatime 0 0    <br />none /dev/pts devpts rw,nosuid,noexec,relatime,mode=600,ptmxmode=000 0 0    <br />none /proc/sys/fs/binfmt_misc binfmt_misc rw,nosuid,nodev,noexec,relatime 0 0    <br />none /run tmpfs rw,nosuid,noexec,relatime,size=419432k,mode=755 0 0    <br />none /run/lock tmpfs rw,nosuid,nodev,noexec,relatime,size=5120k 0 0    <br />none /run/shm tmpfs rw,relatime 0 0</p>
<p>&#160;</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image2.png"><img title="image" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb2.png" width="675" height="152" /></a> </p>
<p>&#160;</p>
<p>&#160;</p>
<p>df 可以查看当前系统的已挂在的磁盘信息：</p>
<p>&#160;</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image3.png"><img title="image" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb3.png" width="547" height="109" /></a> </p>
<p>&#160;</p>
<p>fdisk &ndash;l&#160; 查看系统所有已分区和未分区的磁盘， 并能划分磁盘分区。</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image4.png"><img title="image" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb4.png" width="595" height="328" /></a> </p>
<p>&#160;</p>
<p>mount &ndash;l 也能查看当前系统的挂载情况</p>
<p>&#160;</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image5.png"><img title="image" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb5.png" width="648" height="161" /></a> </p>
<p>原文来自 blog.byneil.com</p>

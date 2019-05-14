---
layout: post
status: publish
published: true
title: vmware player 调整虚拟磁盘的大小
author: Neil




author_login: Neil

wordpress_id: 442
wordpress_url: https://blog.byneil.com/?p=442
date: '2014-03-08 16:43:02 +0000'
date_gmt: '2014-03-08 08:43:02 +0000'
categories:
- Windows
tags:
- vmware
- player
comments: []
---
<p>用vmware player的虚拟机， 今天要装一个软件，发现虚拟机磁盘不够用了。 于是需要扩展虚拟机的磁盘大小。</p>
<p>虚拟机本来只有一个磁盘，一个分区，就是c盘。在数据不丢失的情况下， 现在想要把c盘变大。</p>
<p>&nbsp;</p>
<p>第一步，需要把虚拟机关机，</p>
<p>记住是要完全关机，而不是休眠。</p>
<p>&nbsp;</p>
<p>第二步，打开虚拟机的设置菜单，选中磁盘选项。</p>
<p>并打开Utilities菜单，找到Expand</p>
<p>&nbsp;</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/clipboard.png"><img title="clipboard" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="clipboard" src="https://blog.byneil.com/wp-content/uploads/2014/03/clipboard_thumb.png" width="680" height="593"></a></p>
<p>&nbsp;</p>
<p>在弹出的菜单中，输入想要调整的大小：</p>
<p>&nbsp;</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/clipboard5.png"><img title="clipboard[5]" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="clipboard[5]" src="https://blog.byneil.com/wp-content/uploads/2014/03/clipboard5_thumb.png" width="360" height="207"></a></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>之后点击Expand， 然后系统会出现进度条，提示正在扩展。 估计是要更新虚拟磁盘结构。</p>
<p>&nbsp;</p>
<p>稍等之后，就提示成功。</p>
<p>&nbsp;</p>
<p>第三步， 重启虚拟机， 打开我的电脑， 你发现c盘并没有扩大。 </p>
<p>别担心， 需要在管理菜单中扩展一下：</p>
<p>&nbsp;</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image13.png"><img title="image" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb13.png" width="292" height="252"></a> </p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image14.png"><img title="image" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb14.png" width="877" height="399"></a> </p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>我这里已经扩展过了，所以 &ldquo;Extend Volume&rdquo; 是灰色的。&nbsp; </p>
<p>点击扩展之后，你的c盘就变大了。</p>
<p>&nbsp;</p>
<p>blog.byneil.com</p>

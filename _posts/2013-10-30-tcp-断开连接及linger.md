---
layout: post
status: publish
published: true
title: Tcp 断开连接,及linger
author: Neil




author_login: Neil

wordpress_id: 132
wordpress_url: http://blog.byneil.com/?p=132
date: '2013-10-30 10:57:44 +0000'
date_gmt: '2013-10-30 02:57:44 +0000'
categories:
- TCP-Ip协议簇
tags:
- tcp/ip
comments: []
---
<p>tcp 的断开过程:</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2013/10/image.png"><img class="alignnone size-full wp-image-133" alt="image" src="http://blog.byneil.com/wp-content/uploads/2013/10/image.png" width="980" height="692" /></a></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>只有 client (主动发起断开的一方) 在进入 time_wait 之后会等待一个时间, 这个时间就是linger time.</p>
<p>1. 如果设置linger 为 0, &nbsp; 则主动断开的一方 直接发送RST给对方,然后自己立即结束. &nbsp;不会进行4次挥手过程.</p>
<p>2. 默认情况下,linger为1, &nbsp; 主动断开的一方会发送FIN给对方, &nbsp;然后等待进行完整的 4次挥手. &nbsp; 然后, 主动一方会进入一个linger状态, 持续时间为lingertime, &nbsp;如果设置lingertime为0, 则使用系统的默认时间, 这个时间一般是 2MSL. &nbsp; 如果指定了 LingerTime, 则使用lingertime. &nbsp; &nbsp; &nbsp;处于linger状态的socket, 查看状态是 time_wait. 不会进行任何操作. 等lingertime时间到了, 就会自动消失了.</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>

---
layout: post
status: publish
published: true
title: Linux 下如何保存并恢复 firefox 的设置
author: Neil




author_login: Neil

wordpress_id: 1183
wordpress_url: https://blog.byneil.com/?p=1183
date: '2017-03-25 20:20:08 +0000'
date_gmt: '2017-03-25 12:20:08 +0000'
categories:
- 其他分类
tags: []
comments: []
---
<p>要自动化firefox的一些操作, 除了 firefox 的 一些命令行参数: http://kb.mozillazine.org/Command_line_arguments 外, 可以修改一份 firefox 的设置并保存, 然后在新的机器上,只需要先恢复设置, 再启动 firefox.</p>
<p>firefox 的设置在一个叫 profile 的 文件夹中. 文件夹位置在这里:<br />
http://kb.mozillazine.org/Profile_folder</p>
<p>只需要保存这个目录, 然后复制到新机器的相同位置即可.</p>

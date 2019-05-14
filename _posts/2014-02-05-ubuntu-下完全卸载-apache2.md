---
layout: post
status: publish
published: true
title: Ubuntu 下完全卸载 apache2
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 260
wordpress_url: http://blog.byneil.com/?p=260
date: '2014-02-05 18:34:20 +0000'
date_gmt: '2014-02-05 10:34:20 +0000'
categories:
- linux
tags: []
comments: []
---
<p>&nbsp;</p>
<p>Ubuntu自带 apache2 有时候很讨厌。 有时候需要先卸载了再装别的。</p>
<p>&nbsp;</p>
<p>这里找了一个完全卸载的方法:</p>
<p>&nbsp;</p>
<div class="wlWriterEditableSmartContent" id="scid:f32c3428-b7e9-4f15-a8ea-c502c7ff2e88:ace5be1c-f0af-40f6-bac7-f5db25770f5d" style="float: none; margin: 0px; display: inline; padding: 0px;">
<pre class="brush: bash;">sudo apt-get autoremove apache2 -y
sudo apt-get remove apache* -y
sudo apt-get --purge remove apache-common -y
sudo apt-get --purge remove apache -y
sudo find /etc -name "*apache*" |xargs  rm -rf 
sudo rm -rf /var/www
sudo rm -rf /etc/libapache2-mod-jk
dpkg -l |grep apache2|awk &#039;{print $2}&#039;|xargs dpkg -P</pre>
</div>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>have fun.</p>

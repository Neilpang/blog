---
layout: post
status: publish
published: true
title: ubuntu 安装 hubicfuse
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 458
wordpress_url: https://blog.byneil.com/?p=458
date: '2014-03-09 23:40:35 +0000'
date_gmt: '2014-03-09 15:40:35 +0000'
categories:
- linux
tags:
- fuse
- hubicfuse
- cloudfuse
comments: []
---
<p>&nbsp;</p>
<p>如果你没有gcc，请先安装gcc：</p>
<div class="csharpcode">
<pre class="alt">apt-get  install  build-essential</pre>
</div>
<p>&nbsp;</p>
<p>1. 从github上clone源码：</p>
<p><a title="https://github.com/TurboGit/hubicfuse" href="https://github.com/TurboGit/hubicfuse">https://github.com/TurboGit/hubicfuse</a></p>
<p><a title="https://github.com/TurboGit/hubicfuse.git" href="https://github.com/TurboGit/hubicfuse.git">https://github.com/TurboGit/hubicfuse.git</a></p>
<p>2.执行./conbigure</p>
<p>configure: error: 'Unable to find libxml2. Please make sure library and header files are installed.'</p>
<p>缺少 libxml2</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum">   1:  </span>apt-get install libxml2-dev</pre>
</div>
<p>&nbsp;</p>
<p>然后，发现还是缺少libxml2</p>
<p>找到这个帖子：</p>
<p><a title="https://github.com/redbo/cloudfuse/issues/20" href="https://github.com/redbo/cloudfuse/issues/20">https://github.com/redbo/cloudfuse/issues/20</a></p>
<p>&nbsp;</p>
<div class="csharpcode">
<pre class="alt">apt-get install pkg-config</pre>
</div>
<p>&nbsp;</p>
<p>然后：</p>
<p>configure: error: 'Unable to find libcurl. Please make sure library and header files are installed.'</p>
<div class="csharpcode">
<pre class="alt">apt-get install libcurl4-openssl-dev</pre>
</div>
<p>&nbsp;</p>
<p>然后：</p>
<div class="csharpcode">
<pre class="alt">apt-get install  libcurl3  libcurl3-openssl-dev  libcurl3-dev</pre>
</div>
<p>&nbsp;</p>
<p>然后缺少： libfuse</p>
<p>configure: error: 'Unable to find libfuse.&nbsp; Please make sure library and header files are installed.'</p>
<div class="csharpcode">
<pre class="alt">apt-get install libfuse-dev</pre>
</div>
<p>&nbsp;</p>
<p>缺少json：</p>
<p>找到这个网址:</p>
<p><a title="http://webcache.googleusercontent.com/search?q=cache:XxNdg6n75boJ:linuxprograms.wordpress.com/2010/05/20/install-json-c-in-linux/+&amp;cd=6&amp;hl=en&amp;ct=clnk" href="http://webcache.googleusercontent.com/search?q=cache:XxNdg6n75boJ:linuxprograms.wordpress.com/2010/05/20/install-json-c-in-linux/+&amp;cd=6&amp;hl=en&amp;ct=clnk">http://webcache.googleusercontent.com/search?q=cache:XxNdg6n75boJ:linuxprograms.wordpress.com/2010/05/20/install-json-c-in-linux/+&amp;cd=6&amp;hl=en&amp;ct=clnk</a></p>
<p>先update一下：</p>
<div class="csharpcode">
<pre class="alt">apt-get update</pre>
</div>
<p>&nbsp;</p>
<div class="csharpcode">
<pre class="alt">apt-get install libjson0 libjson0-dev</pre>
</div>
<p>&nbsp;</p>
<p>然后 ./configure</p>
<p>&nbsp;</p>
<p>ok, 搞定。</p>
<p>&nbsp;</p>
<p>然后：</p>
<div class="csharpcode">
<pre class="alt">make</pre>
<pre>make install</pre>
</div>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>然后，新建，并编辑 ~/.cloudfuse&nbsp; 文件</p>
<p>&nbsp;</p>
<p>然后：</p>
<div class="csharpcode">
<pre class="alt">mkdir /mnt/hubic</pre>
</div>
<p>执行：</p>
<div class="csharpcode">
<pre class="alt crayon-selected">hubicfuse /mnt/hubic -o noauto_cache,sync_read,allow_other</pre>
</div>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>如果是openvz的虚拟机，会出现这个错误。&nbsp; 可能是主机没有添加fuse模块，需要写信给客服，让打开fuse模块，不是所有的厂商都愿意给你打开fuse。</p>
<p>fuse: device not found, try 'modprobe fuse' first</p>
<p>&nbsp;</p>
<p>如果是xen的服务器，就已经成功了。</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/03/image16.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb16.png" alt="image" width="880" height="40" border="0" /></a></p>

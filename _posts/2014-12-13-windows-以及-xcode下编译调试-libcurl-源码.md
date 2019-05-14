---
layout: post
status: publish
published: true
title: Windows 以及 Xcode下编译调试 libcurl 源码
author: Neil




author_login: Neil

wordpress_id: 1022
wordpress_url: https://blog.byneil.com/?p=1022
date: '2014-12-13 15:51:15 +0000'
date_gmt: '2014-12-13 07:51:15 +0000'
categories:
- 系统编程
tags: []
comments: []
---
<p><em>curl</em> 这个工具大家都很熟悉. 前几天因为要跟踪curl的实现细节, 不得不设法搭建curl的调试工程.  我们分别在windows visual studio 和 mac 上的 xcode 下搭建调试.  这里记录一些细节,分享出来.</p>
<p><em>curl</em> 可以在数十个平台上编译运行.<br />
这是官网地址:  <a href="http://curl.haxx.se/" title="http://curl.haxx.se" target="_blank">http://curl.haxx.se</a></p>
<p>请在这里下载源码: <a href="http://curl.haxx.se/download.html" title="http://curl.haxx.se/download.html" target="_blank">http://curl.haxx.se/download.html</a></p>
<p>下载解压.</p>
<p>一.  Windows 调试:<br />
在 windows 上调试比较简单.  自带工程文件.<br />
直接用 VS2012 打开 \projects\Windows\VC11 里面的 工程文件就可以了。<br />
务必用vs2012 打开。</p>
<p>直接 F10 就可以单步  到 main 函数。</p>
<p>二. Mac Xcode 调试.<br />
我们主要说说 xcode 调试.<br />
大概是因为 *nix 上的编译比较方便, 所以都是使用 makefile 编译. 所以没有自带工程文件.</p>
<p>所幸的是, 开源项目 cmake 可以根据 makefile 和配置文件逆向生成工程文件.  而更好的消息是 curl 早期是支持 cmake 的. 所以我们需要修改一下 curl 的配置文件, 并结合 cmake 来逆向生成 它在 xcode 上的工程文件.</p>
<p>开工.</p>
<p>1) 需要先安装cmake</p>
<p>下载 cmake 源码， http://www.cmake.org/files/v3.1/cmake-3.1.0-rc1.tar.gz<br />
 然后解压， 进去：  ./configure &amp;&amp; make &amp;&amp; make install<br />
一切顺利的话，应该就能成功了。</p>
<p>2) 生成 makefile</p>
<p>第二步, 我们需要生成curl 的 makefile.  进入curl的源码目录:</p>
<pre><code>./configure  --without-libssh2  --with-darwinssl
make
</code></pre>
<p>说明一下, 我们这里指定了两个参数,  我们不需要 curl 支持 ssh, 所以把"--without-libssh2 " 排除.   而且"--with-darwinssl" 指定使用 mac 上的 ssl 实现, 而不需要 openssl.</p>
<p>这样可以减少出错的可能性, 而且, 我们就需要 darwinssl.</p>
<p>3) 生成 xcode 工程.</p>
<p>前面几步都没什么特别的, 这一步是关键, 使用 cnamke 逆向生成工程.</p>
<p>在 curl 的源码根目录中, 有一个文件 "CMakeLists.txt" 这个是逆向生成所必须的配置文件. 这是curl 官方提供的, 但是已经很旧了, 年久失修, 不能工作了.</p>
<p>我们可以先尝试一下:</p>
<pre><code>mkdir -p build/xcode
cd build/xcode
cmake ../../$CURL -G Xcode

</code></pre>
<p>说明一下, 创建一个子目录保存工程. 然后使用命令:</p>
<pre><code>cmake ../../$CURL -G Xcode
</code></pre>
<p>来生成工程.</p>
<p>这一步会报大量的错误在"CMakeLists.txt" 中.</p>
<p>请使用附件中这个我修改过的版本代替源码中的版本, 然后再重复执行上面的命令生成.<br />
<a href="https://blog.byneil.com/wp-content/uploads/2014/12/CMakeLists.txt">CMakeLists.txt</a></p>
<p>工程文件已经生成了.</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/12/x1.png"><img src="https://blog.byneil.com/wp-content/uploads/2014/12/x1.png" alt="x1" width="701" height="289" class="alignnone size-full wp-image-1049" /></a></p>
<p>但是此时打开工程编译不过. 需要先对工程文件做一些修改:<br />
具体请查看下面的命令.</p>
<pre><code>sed -i "" "s/OTHER_LDFLAGS = \"/OTHER_LDFLAGS = \"-framework Cocoa -framework CoreFoundation -framework CoreServices -framework Security -fobjc-link-runtime -framework LDAP/"  CURL.xcodeproj/project.pbxproj

</code></pre>
<p>在工程中添加了一些 link target. 比较简单. 不多说了.</p>
<p>现在设置一下 调试工程, 就可以调试了.</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/12/x2.png"><img src="https://blog.byneil.com/wp-content/uploads/2014/12/x2.png" alt="x2" width="509" height="312" class="alignnone size-full wp-image-1051" /></a></p>
<p>然后选中 curl 这个target:</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/12/x3.png"><img src="https://blog.byneil.com/wp-content/uploads/2014/12/x3.png" alt="x3" width="463" height="281" class="alignnone size-full wp-image-1053" /></a></p>
<p>然后找到 main 函数. 就可以单步调试了:<br />
<a href="https://blog.byneil.com/wp-content/uploads/2014/12/x4.png"><img src="https://blog.byneil.com/wp-content/uploads/2014/12/x4.png" alt="x4" width="783" height="656" class="alignnone size-full wp-image-1055" /></a></p>
<p>欢迎访问我的个人独立博客: <a href="https://blog.byNeil.com" target="_blank">http://byNeil.com</a></p>

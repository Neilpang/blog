---
layout: post
status: publish
published: true
title: Nodejs 的 c++ module 如何正确链接到 OpenSSL
author: Neil




author_login: Neil

wordpress_id: 599
wordpress_url: https://blog.byneil.com/?p=599
date: '2014-08-02 17:24:03 +0000'
date_gmt: '2014-08-02 09:24:03 +0000'
categories:
- nodejs
tags:
- node
- nodejs
comments: []
---
<p>事情的起因是这样的， 因为某些原因， 最近在写 Nodejs 的 c++ module， 然后在js这边调用。&nbsp; 网络通信自然离不开ssl， 于是需要链接到Openssl的库。</p>
<p>我们本来的期望是，需要用户安装有Openssl的运行库， 然后我们的c++ module 动态链接到Openssl的so库上来运行。&nbsp;</p>
<p>起初一切看起来还不错，直到我们发现这个openssl的函数不能工作：</p>
<pre><code> PKCS7_sign()
</code></pre>
<p>我们发现：</p>
<blockquote>
<ol>
<li>如果我们的 c++ 模块与Openssl库动态链接的话, 编译都没问题. 但是运行会出现: PKCS7_sign 符号无法找到的错误.</li>
<li>如果我们的 c++ 模块与Openssl库静态链接的话, 编译也没问题, 但是运行时,调用这个函数的地方没有效果, 这个函数返回值是 0. 按照文档表示出现错误, 但是用 Openssl的函数 <code>ERR_get_error</code> 获取错误码也是0. 表示没有错误码.</li>
</ol>
</blockquote>
<p>在linux上是这样, 那在Mac上呢? 用Mac试了一下, 发现Mac没有问题. 于是,想到这可能是Nodejs的一个bug. 然后就去 Nodejs 给它报了一个bug: &#91;https://github.com/joyent/node/issues/8026&#93;&#91;1&#93;</p>
<p>同时, google上搜索了 <code>nodejs linking to openssl</code> 类似的关键字.</p>
<p>找到这样几篇文章:<br />
https://github.com/TooTallNate/node-gyp/wiki/Linking-to-OpenSSL</p>
<p>https://github.com/joyent/node/issues/3915</p>
<p>http://serverfault.com/questions/338092/how-can-i-build-node-js-using-static-libssl-and-crypto-libraries</p>
<p>https://github.com/robhawkes/node-extension/issues/1</p>
<p>通过搜索, 我们发现, 原来Nodejs自己也使用了Openssl 库, 推测nodejs自己的crypto模块也是使用Openssl lib实现的. 这点从Nodejs的源码中就能发现, 它包含了最新的Openssl的全部源码.</p>
<p>其中写上面第一篇文章: https://github.com/TooTallNate/node-gyp/wiki/Linking-to-OpenSSL 的那个帅哥是Nodejs的开发人员.</p>
<p>基本结论:</p>
<blockquote>
<ol>
<li>Nodejs 自己使用了Openssl</li>
<li>在Nodejs 0.6之前, Nodejs是动态链接到 Openssl 库的. 而之后的版本都是静态链接的.</li>
</ol>
</blockquote>
<p>这时发现 Node 那边已经回复我的bug了: https://github.com/joyent/node/issues/8026</p>
<p>Node 解释的原因:</p>
<blockquote>
<p>Node 自己编译之后, 把自己没用到的符号清除, 所以我们在运行时就找不到符号了. 于是他们把这bug 修掉了. 保留了全部符号. 这导致 Node 的体积大了 400k.</p>
</blockquote>
<p>感谢Node的快速回复, 不得不佩服Node的活跃程度. 赞.</p>

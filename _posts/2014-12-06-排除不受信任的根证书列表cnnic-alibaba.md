---
layout: post
status: publish
published: true
title: 排除不受信任的根证书列表:cnnic, alibaba
author: Neil




author_login: Neil

wordpress_id: 1035
wordpress_url: https://blog.byneil.com/?p=1035
date: '2014-12-06 14:17:50 +0000'
date_gmt: '2014-12-06 06:17:50 +0000'
categories:
- 其他分类
tags: []
comments: []
---
<p>今天心血来潮, 检查一下我的根证书列表:</p>
<pre><code>certmgr.msc
</code></pre>
<p>排除了一些垃圾流氓证书到不受信任的列表, 以下是我的列表:</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/12/a.png"><img src="https://blog.byneil.com/wp-content/uploads/2014/12/a.png" alt="a" width="1160" height="656" class="alignnone size-full wp-image-1037" /></a></p>
<p>其中 'CNNIC ROOT' 自不必多说.</p>
<p>其中有三个证书值得一说:</p>
<ol>
<li>首先说说前两个,  一个是 'Alibaba.com Corporation Root CA', 另一个是: 'ALIPAY_ROOT'.  作为一个第三方的电子商务公司, 我实在想不通他为什么要在我的机器里面装入 根证书.  而且可以看到这两个根证书的用途都是 "所有".  就是权限最大的根证书. 不光可以做普通证书做的事,比如签名,加密,时间戳等等.  甚至还能签发次级CA. </li>
</ol>
<p>首先, alibaba 网站上使用的证书都是 来自第三方的verisign的. 这没有任何问题.  那他还要给我装权限如此之高的根证书的目的是什么?  除了耍流氓还有什么解释.  你当用户的电脑是韭菜园子吗?  你视用户的安全如空气吗?   有一天要逼我在虚拟机中使用 支付宝吗?  去年买了个包. 流氓.</p>
<ol>
<li>再说说另一个, 就是图中的'ROOTCA', 名字看起来很唬人. 以为是正规的根证书.<br />
但是我们点开详细信息看看:<br />
<a href="https://blog.byneil.com/wp-content/uploads/2014/12/b.png"><img src="https://blog.byneil.com/wp-content/uploads/2014/12/b.png" alt="b" width="484" height="667" class="alignnone size-full wp-image-1039" /></a></li>
</ol>
<p>发现他的 <em>C=CN</em></p>
<p>原来, 这货和 CNNIC 是一路货色.果断禁用之.</p>
<p>最后分享一下禁用方法.<br />
在"受信任的根证书颁发机构"中找到他们之后, 不能直接删除, 删除之后,有些会自动安装比如alibaba, 有些会通过windows自动更新重新装入, 比如CNNICroot.   正确的做法是, 用鼠标把他们拖到下面的 "不信任的证书" 分支里面.<br />
这样就算重新安装了, 他们仍然是不受信的.</p>
<p>我很喜欢淘宝, alibaba, 支付宝, 也是马云的粉丝.  但是,无论是谁,耍流氓就不行. 让流氓证书滚粗.</p>

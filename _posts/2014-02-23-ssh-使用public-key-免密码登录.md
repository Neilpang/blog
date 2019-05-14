---
layout: post
status: publish
published: true
title: ssh 使用public key 免密码登录
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 356
wordpress_url: http://blog.byneil.com/?p=356
date: '2014-02-23 19:58:46 +0000'
date_gmt: '2014-02-23 11:58:46 +0000'
categories:
- linux
tags: []
comments: []
---
<p>&#160;</p>
<p>第一步，生成自己公钥， 私钥</p>
<p>&#160;</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum">   1:  </span>ssh-keygen -t rsa</pre>
<pre><span class="lnum">   2:  </span>&#160;</pre>
<pre class="alt"><span class="lnum">   3:  </span>root@yjlml:~# ssh-keygen -t rsa</pre>
<pre><span class="lnum">   4:  </span>Generating <span class="kwrd">public</span>/<span class="kwrd">private</span> rsa key pair.</pre>
<pre class="alt"><span class="lnum">   5:  </span>Enter file <span class="kwrd">in</span> which to save the key (/root/.ssh/id_rsa): </pre>
<pre><span class="lnum">   6:  </span>Enter passphrase (empty <span class="kwrd">for</span> no passphrase): </pre>
<pre class="alt"><span class="lnum">   7:  </span>Enter same passphrase again: </pre>
<pre><span class="lnum">   8:  </span>Your identification has been saved <span class="kwrd">in</span> /root/.ssh/id_rsa.</pre>
<pre class="alt"><span class="lnum">   9:  </span>Your <span class="kwrd">public</span> key has been saved <span class="kwrd">in</span> /root/.ssh/id_rsa.pub.</pre>
<pre><span class="lnum">  10:  </span>The key fingerprint <span class="kwrd">is</span>:</pre>
<pre class="alt"><span class="lnum">  11:  </span>3e:6e:d9:12:7c:f8:6f:18:f6:65:cb:6e:16:4f:83:43 root@yjlml</pre>
<pre><span class="lnum">  12:  </span>The key's randomart image <span class="kwrd">is</span>:</pre>
<pre class="alt"><span class="lnum">  13:  </span>+--[ RSA 2048]----+</pre>
<pre><span class="lnum">  14:  </span>|                 |</pre>
<pre class="alt"><span class="lnum">  15:  </span>|                 |</pre>
<pre><span class="lnum">  16:  </span>|                 |</pre>
<pre class="alt"><span class="lnum">  17:  </span>|             E   |</pre>
<pre><span class="lnum">  18:  </span>|       .S.  . .  |</pre>
<pre class="alt"><span class="lnum">  19:  </span>|       .+ +  o+..|</pre>
<pre><span class="lnum">  20:  </span>|        oB + +.=.|</pre>
<pre class="alt"><span class="lnum">  21:  </span>|       .+.+ o = .|</pre>
<pre><span class="lnum">  22:  </span>|       ... o.+.  |</pre>
<pre class="alt"><span class="lnum">  23:  </span>+-----------------+</pre>
</div>
<style type="text/css">
.csharpcode, .csharpcode pre<br />
{<br />
	font-size: small;<br />
	color: black;<br />
	font-family: consolas, "Courier New", courier, monospace;<br />
	background-color: #ffffff;<br />
	/*white-space: pre;*/<br />
}<br />
.csharpcode pre { margin: 0em; }<br />
.csharpcode .rem { color: #008000; }<br />
.csharpcode .kwrd { color: #0000ff; }<br />
.csharpcode .str { color: #006080; }<br />
.csharpcode .op { color: #0000c0; }<br />
.csharpcode .preproc { color: #cc6633; }<br />
.csharpcode .asp { background-color: #ffff00; }<br />
.csharpcode .html { color: #800000; }<br />
.csharpcode .attr { color: #ff0000; }<br />
.csharpcode .alt<br />
{<br />
	background-color: #f4f4f4;<br />
	width: 100%;<br />
	margin: 0em;<br />
}<br />
.csharpcode .lnum { color: #606060; }</style>
<p>&#160;</p>
<p>中间连续按几次回车， 使用默认文件名， 并不输入密码。</p>
<p>&#160;</p>
<p>二， 把你的公钥，拷贝到远程机器的&#160; &ldquo;~/.ssh/authorized_keys&rdquo; 文件中。</p>
<p>刚才生成的公钥在： ~/.ssh/id_rsa.pub，&#160; 里面是文本，直接复制出来，粘贴到远程的&ldquo;~/.ssh/authorized_keys&rdquo; 文件中即可。 如果没有这个文件，自己新建一个。</p>
<p>这个文件可以保存多个公钥，只需要连续放在里面就可以了。</p>
<p>拷贝的方法有很多。自己想办法。</p>
<p>&#160;</p>
<p>拷贝完成之后，重启一下ssh服务。</p>
<p>service ssh&#160; restart</p>
<p>&#160;</p>
<p>然后测试是否成功：</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum">   1:  </span>root@yjlml:~# ssh root@190.***.***.***  -p 22   ls</pre>
</div>
<style type="text/css">
.csharpcode, .csharpcode pre<br />
{<br />
	font-size: small;<br />
	color: black;<br />
	font-family: consolas, "Courier New", courier, monospace;<br />
	background-color: #ffffff;<br />
	/*white-space: pre;*/<br />
}<br />
.csharpcode pre { margin: 0em; }<br />
.csharpcode .rem { color: #008000; }<br />
.csharpcode .kwrd { color: #0000ff; }<br />
.csharpcode .str { color: #006080; }<br />
.csharpcode .op { color: #0000c0; }<br />
.csharpcode .preproc { color: #cc6633; }<br />
.csharpcode .asp { background-color: #ffff00; }<br />
.csharpcode .html { color: #800000; }<br />
.csharpcode .attr { color: #ff0000; }<br />
.csharpcode .alt<br />
{<br />
	background-color: #f4f4f4;<br />
	width: 100%;<br />
	margin: 0em;<br />
}<br />
.csharpcode .lnum { color: #606060; }</style>
<p>这里故意指定了一个端口22， 其实可以省略的。如果你的ssh端口不是22可以这样指定。</p>
<p>这句话是在远程执行一个 命令 ls， 看能不能执行成功。</p>
<p>如果能看到ls的结果。那就是成功了。</p>
<p>&#160;</p>
<p>&#160;</p>
<p>三， 错误处理</p>
<p>如果访问不成功，请检查远程机器的 &ldquo;/etc/ssh/sshd_config &rdquo;文件：</p>
<p>找到其中的这一段。</p>
<p><span class="lnum"> </span></p>
<div class="csharpcode">
<pre><span class="lnum">   6:  </span>RSAAuthentication yes</pre>
<pre class="alt"><span class="lnum">   7:  </span>PubkeyAuthentication yes</pre>
<pre><span class="lnum">   8:  </span>#AuthorizedKeysFile    %h/.ssh/authorized_keys</pre>
</div>
<style type="text/css">
.csharpcode, .csharpcode pre<br />
{<br />
	font-size: small;<br />
	color: black;<br />
	font-family: consolas, "Courier New", courier, monospace;<br />
	background-color: #ffffff;<br />
	/*white-space: pre;*/<br />
}<br />
.csharpcode pre { margin: 0em; }<br />
.csharpcode .rem { color: #008000; }<br />
.csharpcode .kwrd { color: #0000ff; }<br />
.csharpcode .str { color: #006080; }<br />
.csharpcode .op { color: #0000c0; }<br />
.csharpcode .preproc { color: #cc6633; }<br />
.csharpcode .asp { background-color: #ffff00; }<br />
.csharpcode .html { color: #800000; }<br />
.csharpcode .attr { color: #ff0000; }<br />
.csharpcode .alt<br />
{<br />
	background-color: #f4f4f4;<br />
	width: 100%;<br />
	margin: 0em;<br />
}<br />
.csharpcode .lnum { color: #606060; }</style>
<p>&#160;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image37.png"><img title="image" style="border-top: 0px; border-right: 0px; border-bottom: 0px; border-left: 0px; display: inline" border="0" alt="image" src="http://blog.byneil.com/wp-content/uploads/2014/02/image_thumb36.png" width="644" height="307" /></a> </p>
<p>&#160;</p>
<p>注意，必须开启 公钥认证， 检查公钥文件。</p>
<p>&#160;</p>
<p>改完之后，重启远程ssh服务： service ssh&#160; restart</p>

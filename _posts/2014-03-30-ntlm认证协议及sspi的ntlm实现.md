---
layout: post
status: publish
published: true
title: NTLM认证协议及SSPI的NTLM实现
author: Neil




author_login: Neil

wordpress_id: 542
wordpress_url: http://blog.byneil.com/?p=542
date: '2014-03-30 16:00:30 +0000'
date_gmt: '2014-03-30 08:00:30 +0000'
categories:
- NTLM
tags:
- NTLM
- NTLM session security
- EPA
comments: []
---
<p>没错，NTLM就是微软应用最广泛的认证协议之一。 NTLM是NT LAN Manager的缩写，这也说明了协议的来源。NTLM 是 Windows NT 早期版本的标准安全协议。Windows 2000内置三种基本安全协议之一。 NTLM适用范围非常广，既可用于域内的认证服务， 也可用于没有AD的环境，让两台独立电脑相互认证。</p>
<p>你可能知道NTLM可以认证用户身份，但是你可能不知道NTLM可以提供会话安全服务（NTLM Session security）。&nbsp;</p>
<p>本文也会分成几篇来写， 现在计划可能包含下面几个大方面的内容：</p>
<h2>1.&nbsp; NTLM协议的认证</h2>
<p>包括 NTLMv1&nbsp; 和 NTLMv2 两个版本。&nbsp; 涉及到NTLM的认证全过程，以及NTLM 的 EPA（Extended Protection for Authentication）实现。</p>
<h2>2.  NTLM Session Security</h2>
<p>即NTLM的会话安全，或者说是NTLM的SSPI实现。 这一部分内容可能不是很常用，能找到的资料也比较少。 这里先简单的说两句。 我们都知道NTLM可以用于认证，实际上，认证过后，NTLM还能提供安全服务。 比如提供对后续通信的安全签名，防篡改，或者对后续通信进行安全可信赖加密，防止被窃听。这一部分实际上是微软SSPI服务内容。 SSPI是Security Support Provider Interface（Microsoft安全支持提供器接口）。&nbsp; SSPI是一个安全框架，很多协议都对它有实现。 比如我们熟知的Kerberos认证协议，它也有SSPI实现的部分。</p>
<h2>3.  NTLM消息的实例</h2>
<p>这一部分，我们会找一个具体的实例来解释认证的全过程。</p>
<hr />
<p>值得注意的是 NTLM 现在已经不光用于windows平台了，Linux 或者 java 平台也可以进行 NTLM 认证，使用 NTLM 的安全服务（但是Java平台对NTLM的会话安全支持貌似有问题）。</p>
<p>作为一个开始，我们先来介绍几个基础概念， 概念清楚了，然后再看技术细节。</p>
<h2>1. 什么是认证。</h2>
<p>认证就是承认和证明的意思。 就是你能证明你的身份。 比如你要访问一个受保护的资源，服务器需要认证你的身份。 你可以声称你是系统管理员， 但是怎么证明你就是管理员呢。 方法很多，这里有个简单直接的方法就是证明你知道管理员的密码。</p>
<h2>认证的问题转化为： <em>怎么证明你知道你所声称的用户的密码？</em>.</h2>
<p>一个简单暴力的证明方法是，让你直接提供密码给服务器，然后服务器去数据库里面比对，看你提供的密码对不对。如果对，认证通过。否则失败。 常见的所谓Windows Forms认证，或者叫做windows basic 认证就是这种方式。 简单直接。 但是密码需要在网络上传输，安全问题就不说了，你懂的。</p>
<p>怎样在不直接提供密码的情况下，间接证明你知道密码呢？&nbsp; NTLM就是干这个的了。</p>
<ul>
<li>什么是NTLM 认证。</li>
</ul>
<p><em>NTLM是一种在不直接提供密码的情况下，间接证明客户端知道用户密码的方法。</em></p>
<p>NTLM认证最常见的应用场景恐怕就是用在浏览器（http协议）上的认证了。 但是实际上，NTLM 只规定了认证的流程，和认证消息格式。 并不跟具体的协议相关。 所以跟http就更没有必然联系了。 浏览器只是在http协议头上携带了NTLM的消息而已，通过了认证。 我们知道http通常是明文的，所以如果直接传输密码非常不安全，NTLM就有效的防止了这个问题。</p>
<p>怎么理解这个问题呢，举个夸张点的例子，&nbsp; 如果不嫌烦，客户端和服务端甚至可以通过传递小纸条的方式传递NTLM消息，来认证身份。 而纸条的内容是明文的， 中间传递者都可以随意查看，但是却无法知道密码，也无法伪造。</p>
<p>现在可以开始技术细节了， 我们还是采取由大及小的方式。 先从整体介绍，再逐步深入。</p>
<ul>
<li>NTLM 的 认证消息，及认证流程。</li>
</ul>
<p>前面说过了，NTLM消息并不和任何传输协议绑定， 它的认证消息理论上可以通过任何方式传递，所以我们的讨论都集中在协议本身，而不去关心下层的传输方式。</p>
<p>我们先看一个图：</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image22.png"><img style="display: inline; border: 0px;" title="image" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb22.png" width="701" height="420" border="0" /></a></p>
<p>NTLM认证共需要三个消息完成：</p>
<p>(1).&nbsp; Type1 消息。 Negotiate 协商消息。</p>
<p>客户端在发起认证时，首先向服务器发送协商消息。 协商需要认证的主体，用户，机器以及需要使用的安全服务等等信息。 并通知服务器自己支持的协议内容，加密等级等等。</p>
<p>&nbsp;</p>
<p>(2). Type2 消息。 Challenge 挑战消息。</p>
<p>服务器在收到客户端的协商消息之后， 会读取其中的内容，并从中选择出自己所能接受的服务内容，加密等级，安全服务等等。 并生成一个随机数challenge, 然后生成challenge消息返回给客户端。</p>
<p>&nbsp;</p>
<p>(3). Type3 消息。 Authenticate认证消息。</p>
<p>客户端在收到服务端发回的Challenge消息之后， 读取熬服务端所支持的内容，和随机数challenge。&nbsp; 决定服务端所支持的内容是否满足自己的要求。 如果满足，则使用自己的密码以及服务器的随机数challenge通过复杂的运算，期间可能需要自己生成一个客户端随机数client challenge也加入运算， 并最终生成一个认证消息。并发回给服务器。</p>
<p>&nbsp;</p>
<p>(4). 服务器在收到 Type3的消息之后， 回经过几乎同样的运算，并比较自己计算出的认证消息和服务端发回来的认证消息是否匹配。如果匹配，则证明客户端掌握了正确的密码，认证成功。 允许客户端使用后续服务。&nbsp; 如果不匹配，则认证失败。</p>
<p>&nbsp;</p>
<ol>
<li>NTLM 认证消息的结构.</li>
</ol>
<p>NTLM的消息很简单， 只有三种， Type1,&nbsp; Type2 和 Type3.&nbsp;&nbsp; 它们都有相似的结构。&nbsp; 认证消息都是二进制的，但是通常我们见到的都是它们的Base64的编码格式。 类似这种：</p>
<pre><code>TlRMTVNTUAADAAAAGAAYAHAAAACSAJIAiAAAAAAAAAAAAAAAGgAaAEgAAAAOAA4AYgAAAAAAAAAAAAAABYKIogAAAAAAAAAPYQBkAG0AaQBuAGkAcwB0AHIAYQB0AG8AcgBOAEUASQBMAC0AUABDALZLpLeO2n6Sx1s9JjrAfQOqf2QsmfTeP9cjC86k7BsjZEsKzjOoYBcBAQAAAAAAAEDGE3IuR88Bqn9kLJn03j8AAAAAAgAEAEsAQQABAAoARgBTAFcARQBCAAQADABrAGEALgBjAG8AbQADABgAZgBzAHcAZQBiAC4AawBhAC4AYwBvAG0ABQAMAGsAYQAuAGMAbwBtAAcACAC0gtdyLkfPAQAAAAAAAAAA
</code></pre>
<p>所以，如果你看到这种形式不要吃惊，把他们用Base64解码即可。</p>
<p>协议中的数字都是采用小端的方式存储。</p>
<p>(1).&nbsp; 消息头</p>
<p>这三种消息都具有相似的消息头：</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image23.png"><img style="display: inline; border: 0px;" title="image" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb23.png" width="456" height="215" border="0" /></a></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>(2) Flags 标记。</p>
<p>这三种消息一般都会携带一个 4字节的int值， 作为消息的Flags。 Flags在三种消息中的位置不一样，所以没有当做消息头来介绍。 不过，这个flags非常重要。这里先单独来介绍：</p>
<p>（下面是我的代码片段，重点标志我做了注释，后面用到的时候会进一步介绍）</p>
<pre><code><br />flagsExps[0x1] = "Unicode";
flagsExps[0x2] = "OEM";
flagsExps[0x4] = "Request Target";
flagsExps[0x8] = "r10(must be zero)";

flagsExps[0x10] = "Negotiate Sign"; // 需要协商签名服务
flagsExps[0x20] = "Negotiate Seal"; // 需要协商加密服务
flagsExps[0x40] = "Negotiate Datagram Style"; //UDP 非连接模式
flagsExps[0x80] = "Negotiate Lan Manager Key"; // 在某些特定的NTLMv1下使用 Lan manager key 后面会详细介绍

//================================
flagsExps[0x100] = "Negotiate Netware(r9 must be zero)";
flagsExps[0x200] = "Negotiate NTLM";
flagsExps[0x400] = "r8(should be zero)";
flagsExps[0x800] = "Negotiate Anonymous"; //使用匿名登录

flagsExps[0x1000] = "Negotiate Domain Supplied";
flagsExps[0x2000] = "Negotiate Workstation Supplied";
flagsExps[0x4000] = "Negotiate Local Call(r7)";
flagsExps[0x8000] = "Negotiate Always Sign";


//===============================
flagsExps[0x10000] = "Target Type is a Domain.";
flagsExps[0x20000] = "Target Type is a Server.";
flagsExps[0x40000] = "Target Type Share(r6)";
flagsExps[0x80000] = "Negotiate NTLM2 Key(EXTENDED_SESSIONSECURITY)"; //使用扩展会话安全

flagsExps[0x100000] = "Request Init Response(NTLMSSP_NEGOTIATE_IDENTIFY)";
flagsExps[0x200000] = "Request Accept Response(r5, must be zero)";
flagsExps[0x400000] = "Request Non-NT Session Key";
flagsExps[0x800000] = "Negotiate Target Info"; //协商 携带 TargetInfo

//===============================
flagsExps[0x1000000] = "r4(must be zero)";
flagsExps[0x2000000] = "NTLMSSP_NEGOTIATE_VERSION(协商携带操作系统版本号, 一般会忽略此项，仅供调试用途 )";
flagsExps[0x4000000] = "r3(must be zero)";
flagsExps[0x8000000] = "r2(must be zero)";

flagsExps[0x10000000] = "r1(must be zero)";
flagsExps[0x20000000] = "Negotiate 128"; // 协商128位加密
flagsExps[0x40000000] = "Negotiate Key Exchange"; //协商交换key， 在会话安全中，使用交换key来加密内容，而不是直接使用会话key
flagsExps[0x80000000] = "Negotiate 56"; //协商56位加密

</code></pre>
<p>(3). Type 1 消息</p>
<p>我这里使用下面<a href="http://davenport.sourceforge.net/ntlm.html" target="_blank">参考文档2</a>中的一个图来介绍：</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image24.png"><img style="display: inline; border: 0px;" title="image" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb24.png" width="771" height="332" border="0" /></a></p>
<p>&nbsp;</p>
<p>a . 前面首先是 8个字节+4个字节的 协议头。前面已经介绍过了。注意，消息类型为1</p>
<p>b . 然后是四个字节的Flags。 前面也介绍过了。&nbsp; 这个Flags中表达了客户端想要使用的NTLM的认证服务， 以及客户端自己所支持的服务。</p>
<p>&nbsp;</p>
<p>c. 然后，是若干个可选的security buffer。</p>
<p>注意上图中的 security buffer， 安全缓冲区。 它是一个8字节固定大小的结构体。它看起来像是这个样子：</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image25.png"><img style="display: inline; border: 0px;" title="image" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb25.png" width="547" height="186" border="0" /></a></p>
<p>它其实是一个缓冲区指针。 它指向一个区域， 这个区域相对于Type1消息起始的偏移量为 offset， 这个区域的大小长度为 MaxLength, 其中的有效使用大小为：Length.</p>
<p>&nbsp;</p>
<p>如果 Flags 中包含 0x1000 标记， 则需要提供域。把域名写入到这个缓冲区中。 字符集由Flags中的 OEM或者Unicode决定。</p>
<p>&nbsp;</p>
<p>如果Flags中包含0x2000标记，则需要提供workstation的名字。&nbsp; 方法同上。</p>
<p>&nbsp;</p>
<p>d. 然后是8字节的操作系统版本号。</p>
<p>如果Flags中包含0x2000000， 则需要提供 操作系统的版本号。</p>
<p>操作系统版本号的写法，请参考 <a href="https://blog.byneil.com/wp-content/uploads/2014/03/MS-NLMP-NT-LAN-Manager-NTLM-Authentication-Protocol-Specification-v20110610-2.pdf" target="_blank">参考文档1</a> 的 2.2.2.10 节。</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image26.png"><img style="display: inline; border: 0px;" title="image" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb26.png" width="654" height="190" border="0" /></a></p>
<p>&nbsp;</p>
<p>e. 然后，就是携带的前面security buffer中所指向的数据了。</p>
<p>&nbsp;</p>
<p>在Type1的消息中，只有前面的 a 和 b项，是必须的， 后面的都是可选的。</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>(4) Type2 消息</p>
<p>服务端在收到 Type1消息之后， 会生成Type2消息返回给客户端。</p>
<p>这个也比较简单，我们还是用 参考文档2 中的一个图来介绍：</p>
<p>&nbsp;</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image27.png"><img style="display: inline; border: 0px;" title="image" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb27.png" width="808" height="375" border="0" /></a></p>
<p>&nbsp;</p>
<p>a . 前面首先还是 8个字节+4个字节的 协议头。前面已经介绍过了。 注意此时的消息类型已经是 2了。</p>
<p>b . 然后是四个字节的Flags。 前面也介绍过了。&nbsp; 这个Flags中表达了服务端所能接受的服务。</p>
<p>&nbsp;</p>
<p>c. 上图中的TargetName 是要访问的机器名。 存储方法是security buffer. 前面介绍过了，不多说了。</p>
<p>&nbsp;</p>
<p>d. Flags 不多说了。</p>
<p>&nbsp;</p>
<p>e. Challenge, 这是一个服务端生成的8字节的随机数， 客户端会用来计算key. 后面会详细介绍用法。</p>
<p>&nbsp;</p>
<p>f. Context, 这是一个8字节的值，其实是两个连续的32位值。 表示一个内部handle。 当服务端发现客户端就是自身的时候（在用一台电脑上），就会生成一个内部的安全handle，填充到这个字段。并且在Flags中设置&nbsp; 0x4000标记。 表示这是一个本地认证。</p>
<p>&nbsp;</p>
<p>g. TargetInformation 也是一个security buffer。 其存储方式前面介绍过。 但是它的buffer中的内容需要介绍一下。基本上，TargetInformation是由一系列连续的 AV_PAIR 结构体连接起来的。</p>
<p>而AV_PAIR的结构如下：</p>
<p>（请参考下面 参考文档1 的 2.2.2.1节）</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image28.png"><img style="display: inline; border: 0px;" title="image" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb28.png" width="684" height="171" border="0" /></a></p>
<p>&nbsp;</p>
<p>i) 先是两个字节的int16值， 表示 id， 表示这个AV结构的类型。</p>
<p>目前有如下类型：</p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image29.png"><img style="display: inline; border: 0px;" title="image" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb29.png" width="616" height="203" border="0" /></a></p>
<p><a href="https://blog.byneil.com/wp-content/uploads/2014/03/image30.png"><img style="display: inline; border: 0px;" title="image" alt="image" src="https://blog.byneil.com/wp-content/uploads/2014/03/image_thumb30.png" width="672" height="507" border="0" /></a></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>ii) 然后是两个字节的int16值，表示值的长度。</p>
<p>&nbsp;</p>
<p>iii) 然后就是值。 它的长度在上面指定的。</p>
<p>&nbsp;</p>
<p>这个TargetInformation 结构非常重要。 后面Type3中还会用到。</p>
<p>&nbsp;</p>
<p>h. 然后是8字节的操作系统版本号。 Type1中已经介绍过了。 不多说了。</p>
<p>&nbsp;</p>
<p>然后后面就是要携带的数据了。</p>
<p>&nbsp;</p>
<p>到这里先告一段落吧， 这里介绍了 NTLM 的概念以及认证流程。 并详细介绍了Type1和Type2消息， 以及Flags每个标志位的意义。 这大概覆盖了NTLM内容的30%左右的内容。&nbsp; Type3消息是整个认证中最为复杂和关键的部分，我们将在下一篇中独立介绍。</p>
<p>希望大家多多支持。</p>
<p>欢迎访问我的个人独立博客： <a href="http://byNeil.com">http://byNeil.com</a> , 希望和大家多多交流共同进步。</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>参考文档：</p>
<ol>
<li>
<p><a title="MS-NLMP-NT-LAN-Manager-NTLM-Authentication-Protocol-Specification" href="https://blog.byneil.com/wp-content/uploads/2014/03/MS-NLMP-NT-LAN-Manager-NTLM-Authentication-Protocol-Specification-v20110610-2.pdf" target="_blank">MS-NLMP-NT-LAN-Manager-NTLM-Authentication-Protocol-Specification</a></p>
</li>
<li>
<p><a title="The NTLM Authentication Protocol and Security Support Provider" href="http://davenport.sourceforge.net/ntlm.html" target="_blank">The NTLM Authentication Protocol and Security Support Provider</a></p>
</li>
<li>
<p><a title="freebsd-freebsd" href="https://github.com/freebsd/freebsd" target="_blank">freebsd-freebsd&nbsp; 源码实现</a></p>
</li>
<li>
<p><a title="Downloading Source Archives - Developer Guide - MDN" href="https://developer.mozilla.org/en-US/docs/Developer_Guide/Source_Code/Downloading_Source_Archives" target="_blank">Mozilla Firefox source code Developer Guide - MDN-firefox 源码</a></p>
</li>
<li>
<p><a title="Heimdal" href="http://www.h5l.org/" target="_blank">Heimdal NTLM 开源工程</a></p>
</li>
<li>
<p><a title="http---ftp.samba.org-pub-unpacked-samba_3_current-librpc-idl-ntlmssp.idl" href="http://ftp.samba.org/pub/unpacked/samba_3_current/librpc/idl/ntlmssp.idl" target="_blank">samba.org-pub-unpacked-samba_3_current-librpc-idl-ntlmssp.idl</a></p>
</li>
</ol>
<p>
<style type="text/css"><!--<br />
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
.csharpcode .lnum { color: #606060; }<br />
--></style></p>

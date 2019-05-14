---
layout: post
status: publish
published: true
title: 使用java keytool 查看,添加,删除 jks 文件
author: Neil




author_login: Neil

wordpress_id: 1131
wordpress_url: https://blog.byneil.com/?p=1131
date: '2016-07-23 21:08:18 +0000'
date_gmt: '2016-07-23 13:08:18 +0000'
categories:
- 其他分类
tags: []
comments: []
---
<p>jks 是 java 的 key store 文件格式. java 提供 keytool 工具操作jks.<br />
keytool 是随 jre 发布的工具. 所以只要你安装了 jre 就有这个工具.<br />
在windows上,是keytool.exe. 在Lunix上,是 keytool</p>
<p>jks 类似于 pfx(p12) 文件, 有密码加密, 可以保存多可key 或者证书等等. key 或者 证书被称为一个 entry, 每个entry有个别名(alias). 操作时需要制定别名.</p>
<h3>list</h3>
<p>查看 jks 的内容:</p>
<pre><code>keytool -list  -keystore  publickeys.jks     -storepass mypassword

keytool -list  -keystore  privatekeys.jks   -storepass mypassword
</code></pre>
<p>keytool 还可以操作pfx 文件.<br />
比如:</p>
<pre><code>keytool -list  -keystore  PartyB.pfx  -storetype pkcs12  -storepass ExampleB  -v 
</code></pre>
<h3>import</h3>
<p>添加证书到 jks 中:</p>
<pre><code>keytool  -importcert  -keystore publickeys.jks  -storepass  mypassword  -alias partyb  -file PartyB.cer
</code></pre>
<p>添加pfx 包含key和证书到 jks 中:</p>
<pre><code>keytool  -importkeystore  -srcstoretype PKCS12 -srckeystore PartyB.pfx  -srcalias  1   -srcstorepass ExampleB   -destkeystore  privatekeys.jks  -deststorepass secrets  -destalias  partyb -destkeypass ExampleB
</code></pre>
<p>注意上面的 <code>-srcalias  1</code>, 指的是 pfx 文件中需要导入到 jks 中的证书的 别名.  如何查看到这个别名呢:</p>
<p>用上面的 list 命令可以查看到 pfx 里面的 alias:</p>
<pre><code>keytool -list  -keystore  PartyB.pfx  -storetype pkcs12  -storepass ExampleB  -v   | grep "Alias"
</code></pre>
<h3>delete</h3>
<p>删除 jks 中的 证书或key</p>
<pre><code><br />keytool  -delete  -keystore publickeys.jks  -storepass  mypassword  -alias partyb
keytool  -delete  -keystore privatekeys.jks  -storepass  mypassword   -alias partyb

</code></pre>

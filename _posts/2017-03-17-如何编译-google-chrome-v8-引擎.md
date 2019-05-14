---
layout: post
status: publish
published: true
title: 如何编译 google chrome v8 引擎
author: Neil




author_login: Neil

wordpress_id: 1153
wordpress_url: https://blog.byneil.com/?p=1153
date: '2017-03-17 20:30:04 +0000'
date_gmt: '2017-03-17 12:30:04 +0000'
categories:
- 其他分类
tags: []
comments: []
---
<p>谷歌 chrome v8 引擎可以嵌入到自己的 c++ 程序中. 一般可以把 v8 编译成 dll, 然后调用.<br />
还可以把 v8 编译成静态库, 静态链接到自己的程序中. 要编译v8的源代码可不是件容易的事.<br />
这里记录下步骤:</p>
<ol>
<li>Pure Windows 7 x64</li>
<li>Install python 2.7</li>
<li>Install git https://git-scm.com/, v8 needs git bash to compile. Then edit the <code>C:\Users\neil\.bashrc</code> file:</li>
</ol>
<pre><code>export DEPOT_TOOLS_WIN_TOOLCHAIN=0
</code></pre>
<ol>
<li>Install VS2013, Express edition is ok. Edit Windows Registry:<br />
<code>HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\VisualStudio\12.0</code>.<br />
Crate a String value: <code>InstallDir</code>, value is: <code>C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\</code><br />
If you already have the value there, just ignore this step.</li>
<li>If you don't have this file: <code>C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\vcvarsall.bat</code>, copy one.</li>
<li>Install VS2015, Express edition is ok.</li>
<li>Install Windows 10 sdk: https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk</li>
<li>Install google depot_tools https://www.chromium.org/developers/how-tos/install-depot-tools</li>
<li>Open git-bash, get v8 source code:</li>
</ol>
<pre><code>fetch v8
</code></pre>
<ol>
<li>Generate vs 2015 project, static lib without snapshot, release edition:</li>
</ol>
<pre><code>cd v8
gn gen out-release/Default --args="v8_static_library=true is_component_build=false v8_use_external_startup_data=false v8_use_snapshot=false is_debug=false" --ide=vs2015
</code></pre>
<p>注意:<br />
1. 第 4 步, 可能是因为 VS2013 express 版有点残, 如果你是pro 或者 ultimate版 可能不需要.<br />
2. 第 8 步, 需要从 google 同步很多工具, 在中国, 你懂得.<br />
3. 第 10 步, 个的参数是生成静态库. 不加,就是动态库. 要查看所有可用参数: "gn args --list" for available build arguments.</p>
<p>refs:<br />
1. https://www.chromium.org/developers/how-tos/install-depot-tools<br />
2. https://v8project.blogspot.com/2015/09/custom-startup-snapshots.html<br />
3. https://chromium.googlesource.com/chromium/src/tools/gn/+/HEAD/docs/reference.md<br />
4. https://stackoverflow.com/questions/38674676/building-and-linking-v8-in-visual-studio</p>

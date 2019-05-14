---
layout: post
status: publish
published: true
title: Delphi/c++builder 中出现 "float operation exception"
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 1180
wordpress_url: https://blog.byneil.com/?p=1180
date: '2017-03-22 22:35:59 +0000'
date_gmt: '2017-03-22 14:35:59 +0000'
categories:
- 其他分类
tags: []
comments: []
---
<p>编译好的 dll 在 c++ builder 64 位中调用,出现 "float operation exception".</p>
<p>尝试了 32位, 没有问题.</p>
<p>尝试了纯console 的 c++ builder 程序, 也没有问题.  只有包含 vcl 的 c++ builder 程序才有问题.</p>
<p>让我感觉到可能是 vcl 改变了 runtime 的某种行为.</p>
<p>跟踪了代码, 异常发生在这一行:<br />
https://github.com/v8/v8/blob/3.20.17/src/conversions.h#L75</p>
<pre><code class="c">inline int FastD2I(double x) {
  return static_cast<int>(x);
}
</code></pre>
<p>当 x 大于 int 的 max 值时,问题出现.</p>
<p>虽然这种转换会导致溢出,  但是怎么会引发异常呢?</p>
<p>然后找到:<br />
https://www.opengl.org/discussion_boards/showthread.php/156817-Disabling-Floating-Point-Exceptions</p>
<p>看起来, opengl 也遇到相同的问题.</p>
<p>然后找到:<br />
https://social.msdn.microsoft.com/Forums/vstudio/en-US/c310909a-fe07-4a4e-92d7-be2e1dc81137/finding-cause-of-floating-point-invalid-operation?forum=vsdebug</p>
<p>最终找到了这篇文章:<br />
https://blogs.msdn.microsoft.com/dougste/2008/11/12/random-and-unexpected-exception_flt_divide_by_zero-and-exception_flt_invalid__operation/</p>
<p>原来 cpu 在操作浮点数时有专门的 fpu 寄存器.</p>
<p>而浮点数如果出现溢出等情况时, 是否导致异常是由一个专门的寄存器 <code>fpcw</code> 控制.</p>
<p>在 windows 平台上, <code>fpcw</code> 的值默认是忽略浮点溢出的异常的.<br />
但是有的程序可能会改变 <code>fpcw</code> 的值, 来引发异常.</p>
<p>解决方案: 执行自己的代码时, 恢复 <code>fpcw</code> 的默认值, 用完再回滚.</p>
<p>头文件:</p>
<pre><code>#include "float.h"
</code></pre>
<p>获得当前 fpcw 的值:</p>
<pre><code class="c">int old_fpcw = _controlfp(0,0)
</code></pre>
<p>设置 <code>fpcw</code> 为 windows 上的默认值:</p>
<pre><code class="c">_controlfp(MCW_EM, MCW_EM);
</code></pre>
<p>用完后, 回滚到原始值:</p>
<pre><code class="c">_controlfp(old_fpcw, _MCW_DN | _MCW_EM |_MCW_RC  )
</code></pre>
<p>另外, 这里还有一篇文章介绍<code>fpu</code> 的工作方式:<br />
http://www.website.masmforum.com/tutorials/fptute/fpuchap1.htm</p>

---
layout: post
status: publish
published: true
title: 解决:Microsoft.PowerShell_profile.ps1 cannot be loaded because the execution
  of scripts is disabled on this system. Please see &ldquo;get-help about_signing&rdquo;
  for more details.
author: Neil




author_login: Neil

wordpress_id: 395
wordpress_url: https://blog.byneil.com/?p=395
date: '2014-03-06 10:02:37 +0000'
date_gmt: '2014-03-06 02:02:37 +0000'
categories:
- Windows
tags:
- powershell
comments: []
---
<p>在执行powershell启动脚本的时候. 会报这个错</p>
<p>Microsoft.PowerShell_profile.ps1 cannot be loaded because the execution of scripts is disabled on this system. Please see &ldquo;get-help about_signing&rdquo; for more details.</p>
<p>原因是因为windows默认启用了安全策略, 禁止启动脚本.</p>
<p>查看策略:</p>
<p><span style="line-height: 1.5em;">PS C:\Windows\System32> Get-ExecutionPolicy</span></p>
<p>Restricted<br />
PS C:\Windows\System32></p>
<p>需要关闭它:</p>
<p>PS C:\Windows\System32> Set-Executionpolicy -ExecutionPolicy Unrestricted</p>
<p>这样就好了.</p>
<p>参考:&nbsp;http://www.christiano.ch/wordpress/2009/07/26/solution-microsoft-powershell_profile-ps1-cannot-be-loaded-because-the-execution-of-scripts-is-disabled-on-this-system-please-see-get-help-about_signing-for-more-details/</p>
<p>&nbsp;</p>

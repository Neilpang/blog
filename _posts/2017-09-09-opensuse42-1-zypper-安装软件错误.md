---
layout: post
status: publish
published: true
title: opensuse:42.1 zypper 安装软件错误
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 1236
wordpress_url: https://blog.byneil.com/?p=1236
date: '2017-09-09 13:27:22 +0000'
date_gmt: '2017-09-09 05:27:22 +0000'
categories:
- linux
tags: []
comments: []
---
<p>zypper update 时出现以下错误:</p>
<pre class="line-numbers prism-highlight" data-start="1"><code class="language-null">bash-4.2# zypper update -y
Retrieving repository 'NON-OSS' metadata ---------------------------------------------------------------------------------------------------[\]
File '/media.1/media' not found on medium 'http://download.opensuse.org/distribution/leap/42.1/repo/non-oss/'

Abort, retry, ignore? [a/r/i/? shows all options] (a): a
Retrieving repository 'NON-OSS' metadata ...............................................................................................[error]
Repository 'NON-OSS' is invalid.
[non-oss|http://download.opensuse.org/distribution/leap/42.1/repo/non-oss/] Valid metadata not found at specified URL
Please check if the URIs defined for this repository are pointing to a valid repository.
Warning: Skipping repository 'NON-OSS' because of the above error.
Retrieving repository 'OSS' metadata -------------------------------------------------------------------------------------------------------[|]
File '/media.1/media' not found on medium 'http://download.opensuse.org/distribution/leap/42.1/repo/oss/'

Abort, retry, ignore? [a/r/i/? shows all options] (a): a
Retrieving repository 'OSS' metadata ...................................................................................................[error]
Repository 'OSS' is invalid.
[oss|http://download.opensuse.org/distribution/leap/42.1/repo/oss/] Valid metadata not found at specified URL
Please check if the URIs defined for this repository are pointing to a valid repository.
Warning: Skipping repository 'OSS' because of the above error.

</code></pre>
<p>是因为 <code>oss</code> 和 <code>non-oss</code> 源无效了. 只需要移除这两个源即可.</p>
<pre class="line-numbers prism-highlight" data-start="1"><code class="language-null">zypper rr OSS NON-OSS
</code></pre>

---
layout: post
status: publish
published: true
title: zip 压缩文件名的unicode(utf-8)支持
author: Neil




author_login: Neil

wordpress_id: 135
wordpress_url: http://blog.byneil.com/?p=135
date: '2013-11-01 17:16:10 +0000'
date_gmt: '2013-11-01 09:16:10 +0000'
categories:
- Compression
tags: []
comments: []
---
<p>参见:<a href="http://www.pkware.com/documents/casestudies/APPNOTE.TXT">http://www.pkware.com/documents/casestudies/APPNOTE.TXT</a></p>
<pre>        Bit 11: Language encoding flag (EFS).  If this bit is set,
                the filename and comment fields for this file
                MUST be encoded using UTF-8. (see APPENDIX D)</pre>
<pre>D.1 The ZIP format has historically supported only the original IBM PC character 
encoding set, commonly referred to as IBM Code Page 437.  This limits storing 
file name characters to only those within the original MS-DOS range of values 
and does not properly support file names in other character encodings, or 
languages. To address this limitation, this specification will support the 
following change.</pre>
<p>zip 文件头标志位的第11个位表示是否支持unicode文件名.</p>
<p>如果为0, 表示默认的使用 IBM 437 编码,</p>
<p>如果为1, 表示使用unicode文件名, 并且 &nbsp;文件名一定使用的是utf-8编码.</p>
<p>概括起来, &nbsp;zip格式本来只支持英文 IBM437编码. &nbsp;后来扩展了这个标记. 表示文件名是用utf-8编码表示的.</p>
<p>并且zip只支持utf-8.</p>
<p>&nbsp;</p>

---
tags:
  - wireshark
  - SSLKEYLOGFILE
  - ssl
  - 解密
---


我们知道 wireshark 可以直接解密以 RSA 为密钥交换算法的 ssl 流量. 只需要给 wireshark 设置 ssl 证书私钥就可以了.

但是 RSA 交换算法已经普遍被禁用了, 对于主流的 DH 或者 ECDH 交换算法, wireshark 无法解密.

但是, 工业上的一个实际标准可以试 wireshark 解密这些ssl 链接:

设置  `SSLKEYLOGFILE` 环境变量.

参考: [https://www.comparitech.com/net-admin/decrypt-ssl-with-wireshark/](https://www.comparitech.com/net-admin/decrypt-ssl-with-wireshark/)

简单的讲, firefox 和 chrome 在运行时会检测这个环境变量`SSLKEYLOGFILE`,  它指向一个文件, firefox会把 DH 交换的私钥保存到这个文件里面.

在Wireshark 中, 有个设置可以指向这个文件, 这样 wireshark 就可以动态的在这个文件中查找相应的 DH key, 并成功解密ssl 链接.

目前主流软件都支持这种方法(chrome, firefox, openssl, python, nodejs), 但是支持细节有所差异.

比如, nodejs不使用这个环境变量, 而是在 `tls.TLSSocket` 上暴露一个 `on("keylog", (line)=>{})` 事件, 用户可以接收这个时间, 并保存到文件中.



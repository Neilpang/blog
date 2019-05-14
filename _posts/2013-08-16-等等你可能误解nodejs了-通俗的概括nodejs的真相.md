---
layout: post
status: publish
published: true
title: 等等,你可能误解nodejs了--通俗的概括nodejs的真相
author:
  display_name: Neil
  login: Neil
  email: info@byneil.com
  url: ''
author_login: Neil
author_email: info@byneil.com
wordpress_id: 86
wordpress_url: http://blog.byneil.com/?p=86
date: '2013-08-16 18:12:50 +0000'
date_gmt: '2013-08-16 10:12:50 +0000'
categories:
- nodejs
tags: []
comments: []
---
<p>最近刚把产品从cpp平台迁移到nodejs平台了. &nbsp;很多以前关于nodejs的观念被颠覆了. 这里分享出来, 欢迎大家批评指正.</p>
<p>"nodejs是做服务器端开发的, 它一定和web相关,几乎是用来做网站开发的." &nbsp;这是我之前一直的观念. &nbsp;相信这可能也是很多人对nodejs的初步认识吧. &nbsp;但后来我才发现, 我可能错了.</p>
<p>第一个问题: &nbsp;nodejs到底是什么?</p>
<p><a href="http://nodejs.org/">http://nodejs.org/</a>&nbsp;官方主页上有一段解释: "Node.js is a platform built on Chrome's JavaScript runtime for easily building fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, perfect for data-intensive real-time applications that run across distributed devices."</p>
<p>我们注意其中的几个关键字: &nbsp;nodejs是一个平台, &nbsp;它构建在chrome的v8引擎之上, &nbsp;能简易的构建快速,可扩展的网络应用程序.......</p>
<p>这里官方用的"网络应用程序", &nbsp;整个描述没有提到"web", "server" 等等概念. &nbsp;这段话的描述中, 有两个是重点, 第一,就是chrome的v8引擎. &nbsp;第二, 是事件驱动的非阻塞io模型. &nbsp; 把握住这两点, &nbsp;我觉得就算掌握了nodejs的真谛了.</p>
<p>这么说吧, &nbsp;举个例子, 类比来说, &nbsp;概念上, nodejs相当于.net, &nbsp; jvm 或者 python. &nbsp; 它是一个运行平台, 只不过它运行的是javascript语言而已. &nbsp; 类似地, &nbsp;.net一般运行C#, vb等编译过后的il. &nbsp;而jvm一般运行java编译成的字节码, &nbsp;python一般运行python语言.</p>
<p>你可能要问, 那么nodejs是不是也能实现 .net等等这些平台的功能呢. &nbsp;比如写个窗口桌面程序, 做socket网络通信, 以及访问磁盘文件等等.</p>
<p>恩, 这些问题的答案都是肯定的.</p>
<p>nodejs核心主要是由两部分组成的:</p>
<p>第一, 是v8引擎, 它负责把javascript代码解释成本地的二进制代码运行.</p>
<p>第二, 是libuv, &nbsp;类似windows上的窗口消息机制, 它主要负责订阅和处理系统的各种内核消息. 而且它也实现了消息循环(是不是很耳熟? 没错, 这个几乎就和windows 的窗口消息循环是一个概念.). &nbsp;它的前身是linux上的libev, 专门封装linux上的内核消息机制. &nbsp;后来nodejs重写了它, 并在windows上使用iocp技术重新实现了一遍. &nbsp;所以nodejs现在能跨平台运行在windows上了.</p>
<p>&nbsp;</p>
<p>nodejs其实就是libuv的一个应用而已.</p>
<p>你自己写程序也可以集成libuv进来, 这样你的c++程序就有了消息循环了. 不再是简单main函数了. 你可以订阅系统的事件, 然后当事件发生时, 系统会调用你的回调函数, 就跟windows上的button click事件一样方便. 而且是跨平台的哦. 是不是很酷. &nbsp; &nbsp;你几乎可以订阅所有的系统事件, &nbsp;比如socket事件, 文件读写事件等等.</p>
<p>nodejs简单的说只是把javascript解释成c++的回调, 并挂在libuv消息循环上, 等待处理. &nbsp;这样就实现了非阻塞的异步处理机制.</p>
<p>那么为什么是javascript而不是其他的语言. &nbsp;很简单, 因为javascript的闭包. &nbsp;这非常适合做回调函数. &nbsp;因为我们一般都希望当回调发生时, 它能记住它原来所在的上下文. &nbsp;这就是闭包最好的应用场景.</p>
<p>这里有libuv的详细介绍&nbsp;http://nikhilm.github.io/uvbook/.</p>
<p>好像扯远了, &nbsp;说nodejs的, 怎么扯到libuv了. &nbsp;很简单, 因为 nodejs只是libuv的一个应用. &nbsp;先了解libuv才能了解nodejs的实质和前世今生.</p>
<p>我们再回来说nodejs.</p>
<p>从另一个角度看, &nbsp; 上面的工作都分别由v8和libuv做了. &nbsp;那么nodejs到底做了什么呢? &nbsp; 我们先看一下nodejs的文档:&nbsp;http://nodejs.org/api/</p>
<p>对了,除了用javascript封装libuv框架之外, &nbsp; nodejs就是实现了这些api 功能. &nbsp;这些api大部分是用javascript写的, 也有一部分是c++写的.</p>
<p>这是nodejs官方的仓库,&nbsp;https://github.com/joyent &nbsp;其中有很多nodejs的插件. 有了这些nodejs就可以实现非常丰富的功能了.</p>
<p>作为结尾, 写一个简单的nodejs 常规helloworld 程序.</p>
<p>//test.js</p>
<p>//=================</p>
<p>console.log("hello world!")</p>
<p>//=================</p>
<p>存成test.js. &nbsp;然后运行: node test.js</p>
<p>就能看到效果了.</p>
<p>怎么样, &nbsp;看起来是不是很像python的感觉. &nbsp;但是用的是javascript哦. &nbsp;用这个代替python, 是不是爽死了.</p>
<p>就到这吧. &nbsp;关于libuv, 关于nodejs插件. &nbsp;等等话题, 希望能跟大家沟通交流.</p>
<p>欢迎大家访问我的独立博客: http://byNeil.com</p>
<p>&nbsp;</p>

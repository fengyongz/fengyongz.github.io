---
layout: blog
istop: true
title: "php和cgi、fastcgi、php-fpm之间的关系"
keywords: "php|cgi|fastcgi|php-fpm"
background-image: /style/images/Graph_20180416.png
date:  2018-04-22 17:35
category: php
tags:
- php
- cgi
- php-cgi
- fastcgi
- php-fpm
---
 
# 目的
 
对于不少人来说，对php都停留在使用阶段，对于php的加载了解后也是一知半解。其实这不能怪不认真学习，只是市面上最多的还是应用层次的讲解。
因此，从客户端请求-http服务器转发-脚本服务器处理请求-返回处理到http服务器-返回结果到客户端，这一流程进行图解。而这刚好也阐释了cgi、fastcgi这些听得让人迷糊的名词。

# 流程图

先看下流程图，了解请求的动向。
[![流程图]({{site.url}}/style/images/Graph_20180416.png)]({{site.url}}/style/images/Graph_20180416.png)

# 流程讲解

### 客户端(浏览器)发起请求 到 http服务器

客户端(通常是浏览器)发起GET、POST请求，到达http服务器后：
- 请求静态资源，服务器则直接返回
- 请求需要处理的资源时，例如xx.php, 则由http服务器转给专门的脚本语言处理。

值得注意的是，nginx和apache不同，nginx是直接转出请求给php解释器，apache则是启动时直接把php模块加入apache里。

### http服务器 到 脚本语言程序

这里就涉及到cgi。http服务器和脚本语言程序之间需要一个标准，来规定双方的传输数据方法和格式。cgi(common gateway interface)是一个协议接口，方便所有的脚本语言和http服务器之间进行通信和处理。

那么cgi规定的是什么？具体可以google下cgi，这里简单说就是：
- 传输内容有http请求的头部信息、服务器信息、字符编码等
- 请求过来启动一个cgi解释器，初始化，处理和回传数据，然后关闭

字面上也能看出缺点，cgi的缺点就是每次都需要重新加载，多任务并发时就满负载甚至垮掉。

为了解决这个问题，fastcgi就应运而生。fastcgi简单来说就是为了解决cgi的缺点而提出来的：
- fastcgi解释器常驻与内存，不需要每次都加载
- fastcgi解释器具有进程管理功能，启动时会有1个master和几个worker解释器，常驻内存等待调用
- http服务器传来请求给master，再由master分配给worker，worker处理后继续在内存里接受等待调用

#### php-cgi和php-fpm

php和http服务器之间进行交流的，则是实现cgi或者fastcgi协议的php-cgi和php-fpm程序了。
这里有个要澄清的：
- php-cgi是fastcgi程序
- php-fpm也是fastcgi程序

是的，没看错，php-cgi实现的是fastcgi而不是cgi协议,它是php官方的实现cgi协议标准的程序。那么为什么又会出现php-fpm呢？

php-cgi有缺点，其中包括：
- php.ini 配置文件更改后，不能平滑过渡重启,要kill掉php-cgi
- 不支持动态worker调度，只能一开始指定要起几个worker

因此，php-cgi的问题官方不解决，第三方就开发了php-fpm这个php插件，解决了这两个问题。
- php.ini 配置更改后，master加载reload后，启动新worker
- 动态调度功能，可以根据请求来访压力变化动态增减worker进程数量

后来，php5.3.3就把php-fpm收入官方内核里。

### 脚本解释器 到 http服务器 到客户端

脚本语言程序处理完后，再按标准传输数据回http服务器，最后返回给客户端浏览器，完成请求。

## 参考资料
- [CGI、FastCGI和PHP-FPM关系图解](https://www.awaimai.com/371.html)
- [搞不清FastCgi与PHP-fpm之间是个什么样的关系](https://segmentfault.com/q/1010000000256516)
- [php-cgi和php-fpm有什么关系](https://segmentfault.com/q/1010000008356979)
- [FastCGI](http://www.php-internals.com/book/?p=chapt02/02-02-03-fastcgi)


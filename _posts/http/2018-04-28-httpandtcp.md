---
layout: blog
istop: false
title: "http、tcp的原理和流程"
keywords: "http|tcp"
background-image: /style/images/BlankPortrait_20180428.png
date:  2018-04-28 15:05
category: http
tags:
- http
- tcp
---
 
# 目的
 
了解http请求的经过，梳理http协议，tcp/ip协议等联系和流程。

# 流程图

先看下流程图，了解具体步骤。
[![流程图]({{site.url}}/style/images/BlankPortrait_20180428.png)]({{site.url}}/style/images/BlankPortrait_20180428.png)

# 流程讲解

### 客户端发送http请求

浏览器输入网址，按下enter键后，首先会先经过dns解析域名转化ip。

然后经过tcp的3次握手建立起http连接。

再发起http请求。

### http服务器接收请求后，通过tcp传输数据，完毕后发起http响应

http服务器接收到http请求后，首先发送tcp确认包。

然后切割数据包，经过tcp传输数据包，每个数据包被客户端接收后，都会返回一个tcp确认包，直至数据传输完毕后。

tcp数据传输完毕后，http服务器再发起一个http响应，通知客户端响应状态等。

### http1.1 keep-alive特性能够一次http连接处理多个请求

第一个http请求和相应后，浏览器解析文件会继续发起新的http请求，这个时候tcp建立的http连接仍然存在。

等所有处理完后，客户端和服务器之间通过4次分手来断开连接。


### 参考资料
- [【一次完整的 HTTP 请求与响应涉及了哪些知识？】](https://juejin.im/entry/58ce00c5ac502e00589b4bde)
- [【关于HTTP协议，一篇就够了】](https://www.jianshu.com/p/80e25cb1d81a)
- [【一次完整的HTTP事务是怎样一个过程？】](https://www.linux178.com/web/httprequest.html)
- [【HTTP请求过程-域名解析和TCP三次握手建立链接】](https://www.cnblogs.com/caijh/p/7698819.html)
- [【HTTP请求过程】](http://www.cnblogs.com/caijh/p/7661402.html)
- [【通俗大白话来理解TCP协议的三次握手和四次分手】](https://github.com/jawil/blog/issues/14)
- [【一次完整的HTTP请求与响应涉及了哪些知识？】](https://www.jianshu.com/p/c1d6a294d3c0)
- [【访问Web，tcp传输全过程（三次握手、请求、数据传输、四次挥手）】](https://blog.csdn.net/sinat_21455985/article/details/53508115)
- [【基于TCp的数据包传输过程】](https://blog.csdn.net/suiyuan19840208/article/details/22062657)


---
layout: blog
istop: true
title: "websocket的原理和流程"
keywords: "websocket"
background-image: /style/images/BlankPortrait_20180430.png
date:  2018-04-30 16:35
category: websocket
tags:
- websocket
- php
- http
---
 
# 目的
 
了解websocket的原理和流程，进一步理解http，tcp之间的关系。

# 流程图

先看下流程图，了解具体步骤。
[![流程图]({{site.url}}/style/images/BlankPortrait_20180430.png)]({{site.url}}/style/images/BlankPortrait_20180430.png)

# 流程讲解

### 客户端发送ws请求

ws请求头部带有upgrade字段和升级密钥sec-websocket-key字段。

### ws服务器自动接收并创建socket连接

websocket进程服务器事先已经启动好，监听接口和等待连接。

当接收到请求连接时，自动创建新的socket连接，解析出sec-websocket-key值，然后通过算法升级密钥，再拼接升级头部返回ws响应。

自此，该客户端和ws服务器之间就建立好了一个socket通道连接，两者直接通过tcp进行数据传输，不需要重新再发起请求。

### 参考资料
- [【PHP的Socket编程】](https://www.jianshu.com/p/f671d3895d13)
- [【细说websocket - php篇】](http://www.cnblogs.com/hustskyking/p/websocket-with-php.html)
- [【网页实时聊天之PHP实现websocket】](http://www.cnblogs.com/zhenbianshu/p/6111257.html)
- [【Websocket协议之php实现】](https://www.cnblogs.com/oshyn/p/3593223.html)
- [【微信小程序入门六: WebSocket应用】](https://blog.csdn.net/lecepin/article/details/54632749)
- [【WebSocket的简单介绍及应用】](https://segmentfault.com/a/1190000004649040)
- [【WebSocket协议：5分钟从入门到精通】](https://www.cnblogs.com/chyingp/p/websocket-deep-in.html)
- [【WebSocket 教程】](http://www.ruanyifeng.com/blog/2017/05/websocket.html)
- [【Websocket原理及使用场景】](https://www.36nu.com/post/171)


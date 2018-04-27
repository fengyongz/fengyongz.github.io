---
layout: blog
istop: true
title: "cookie和session的联系和区别"
keywords: "cookie|session|"
background-image: /style/images/BlankPortrait_20180427.png
date:  2018-04-27 16:35
category: 网站应用
tags:
- cookie
- session
- php
- http
---
 
# 目的
 
通过用户的登录会话场景，记录下cookie和session在http请求里的流向和两者之间的联系和区别。

# 流程图

先看下流程图，了解具体步骤。
[![流程图]({{site.url}}/style/images/BlankPortrait_20180427.png)]({{site.url}}/style/images/BlankPortrait_20180427.png)

# 流程讲解

### 客户端发送登录请求

未登录状态下，带有登录表单的用户名、密码等信息发送请求给网站服务器。

### http服务器转发动态请求

收到客户端请求后，http服务器(例如nginx)转发请求给相应的脚本语言程序(例如php-fpm)。

### 脚本语言程序生成会话id

生成唯一的会话id有两种方式，一种是使用官方的生成session方法，另一种是自定义唯一session id来达到类似的会话效果。

#### （-）生成官方session会话id

php.ini 配置文件里有session用于保存到cookie的名字，和session的文件保存路径。

session_start()来生成session id，然后这里会默认包含 set_cookie('PHPSESSID', xxx)。

#### （二）自定义生成sessionid

通过自定义的方法，例如结合时间+ip+随机数等方法，来生成一个唯一的id值，用于充当session会话id。

然后自定义一个cookie name，用来标识和传递给客户端session id。

set_cookie('xxxID', xxx)。

### 脚本语言程序->http服务器->客户端

处理完数据后，生成了唯一会话id，则带着设置cookie的头部字段，一层层往回传递，直至客户端(浏览器)把session id写入cookie里，即完成了登录的操作。

之后客户端再发送请求，就会带上cookie里的会话session id，来以此带到保持会话的状态。


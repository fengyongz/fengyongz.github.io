---
layout: blog
istop: true
title: "mac下homebrew安装php+mysql+nginx"
keywords: "php|cgi|fastcgi|php-fpm"
background-image: /style/images/BlankPortrait_20180502.png
date:  2018-05-02 12:00
category: homebrew
tags:
- php
- homebrew
- mysql
- nginx
- mac
---
 
# 目的
 
记录下mac 10.13 homebrew下安装http服务器(nginx+php+mysql)的步骤和流程。

# 流程图

先看下流程图。
[![流程图]({{site.url}}/style/images/BlankPortrait_20180502.png)]({{site.url}}/style/images/BlankPortrait_20180502.png)

# 流程讲解

### 前提条件

- mac os 10.13.4 high sierra
- 安装homebrew

### brew安装php5.6

brew新版已经把php移到homebrew-core包里，所以不需要再tap别的php源。

	brew install php56
	brew service start|stop|restart php@5.6


利用brew自身的命令来进行服务的部署，service本身就是自启动的守护进程方法。

#### pecl安装php扩展

新版brew把php加入core核心包，同时也把php的扩展移除了，也就是不能再用brew安装php的扩展包。

但是新版brew已经把php安装扩展的官方程序pecl默认安装在php的bin包里，因此：

`/usr/local/opt/php@5.6/bin/pecl info redis`

通过info来查看pecl是否有这个扩展包的信息，或者通过pecl官网来查找也一样。

pecl默认安装的是扩展包的最新版本，可以自己指定扩展包版本(pecl官网查看)

`/usr/local/opt/php@5.6/bin/pecl install memcached-2.2.0`

安装memcached时候提示一些依赖错误，则需要用brew一一解决：

	brew install libmemcached
	brew install pkg-config


### brew安装mysql

`brew install mysql`

### brew安装nginx

	brew install nginx
	[sudo ]brew service start|reload|stop|restart nginx

**nginx更改配置后，测试必须加上sudo，不然加载不到新的配置**


## 参考资料
- [【2018-04-10macOS High Sierra 10.13 搭建 PHP 开发环境】](https://www.jianshu.com/p/e58e8f52da13)
- [【Mac下用Homebrew安装配置nginx+php+mysql（最新实践版）】](https://www.jianshu.com/p/2ba7820883ba)
- [【在Mac上安装pecl 】](https://www.jianshu.com/p/598c0fd84719)
- [【用 PEAR 编译共享 PECL 扩展库 】](http://php.net/manual/zh/install.pecl.pear.php)
- [【php pear / pecl 扩展工具的安装和使用】](https://my.oschina.net/sallency/blog/693150)
- [【Mac PHP使用 pecl 安装扩展】](http://www.cleey.com/blog/single/id/817.html)
- [【Mac 下 Nginx、PHP、MySQL 和 PHP-fpm 的安装和配置】](https://segmentfault.com/a/1190000005090828)
- [【安装php扩展imagick】](https://blog.zmis.me/detail_957)
- [【PHP7 下安装 memcache 和 memcached 扩展】](http://www.lnmp.cn/install-memcache-and-memcached-extends-under-php7.html)


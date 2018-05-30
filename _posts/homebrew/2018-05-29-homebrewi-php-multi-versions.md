---
layout: blog
istop: false
title: "mac下homebrew安装多版本php并存和切换"
keywords: "mac|svn|php|php-version"
background-image: /style/images/default.png
date:  2018-05-29 12:00
category: homebrew
tags:
- svn
- php
- php-version
---
 
# 目的
 
记录下mac 10.13 homebrew下安装多版本php并存和切换的步骤和流程。

# 流程讲解

先查看当前安装：

	brew list
    ... php@5.6 ...

    ps aux|grep php
    ...
    _www               286   0.0  0.1  4412144   8936   ??  S     8:18上午   0:00.07 /usr/local/opt/php@7.0/sbin/php-fpm --nodaemonize

    sudo brew services stop php@5.6

停掉当前php，然后安装php7.0：

    brew install php70
    brew list
    ... php@5.6 php@7.0 ...
    sudo brew services start php@7.0

可以通过`brew services start|stop php@5.6|php@7.0` 来进行php版本切换。

以前brew里有php-version用以方便切换php版本：

    brew install php-version
    source $(brew --prefix php-version)/php-version.sh && php-version 5

**但是自从brew新版把很多第三方包丢弃后，这个方法也就不能再使用了**

## 参考资料
- [【mac os x使用brew安装subversion】](http://lucien-zzy.iteye.com/blog/2359442)


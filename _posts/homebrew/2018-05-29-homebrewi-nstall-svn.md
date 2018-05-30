---
layout: blog
istop: false
title: "mac下homebrew安装svn"
keywords: "mac|svn|subversion"
background-image: /style/images/default.png
date:  2018-05-29 12:00
category: homebrew
tags:
- svn
- subversion
- mac
---
 
# 目的
 
记录下mac 10.13 homebrew下安装svn的步骤和流程。

# 流程讲解

不删除旧svn的前提下：

	brew install svn
    #默认没有安装link在/usr/local/bin里
    /usr/local/opt/subversion/bin/svn --version

brew默认没有把svn的命令放到/usr/local/bin里，可以手动进行`brew link`。

有些brew不建议把安装的命令放在/usr/local/bin里，例如php，必须使用`--force`来强制使用

`brew link`的意思是，把brew安装的文件，做个链接到/usr/local/bin里，例如这里:

    #先找到包名
    brew list
    ... subversion ...
    #进行包的命令链接，会把subversion里的bin目录的命令做link到/usr/local/bin里
    brew link subversion 
    #出现man8报错意外，查看man8的权限是root:admin，里面文件是Parallels Desktop的文件
    #那么修改man8文件夹权限来修复 chown -R von:admin .../man/man8

    ll /usr/local/bin
    ...
    lrwxr-xr-x  1 von   admin    37B  5 30 09:30 svn -> ../Cellar/subversion/1.10.0_1/bin/svn
    lrwxr-xr-x  1 von   admin    42B  5 30 09:30 svnadmin -> ../Cellar/subversion/1.10.0_1/bin/svnadmin
    ...

至此，如果$PATH里是/usr/local/bin放在/usr/bin前解析的话，那么:

    which svn
    /usr/local/bin/svn

    svn --version
    svn, version 1.10.0 (r1827917)
    compiled Apr 22 2018, 07:20:07 on x86_64-apple-darwin17.5.0

brew里安装的程序的bin文件有些会自动然后放置到/usr/local/bin，所以一般来说$PATH里优先于/usr/bin：

    export $PATH
    #如果不是，修改/etc/paths文件
    vim /etc/paths

    /usr/local/bin
    /usr/bin
    ...

## 参考资料
- [【mac os x使用brew安装subversion】](http://lucien-zzy.iteye.com/blog/2359442)
- [【Mac OSX 添加环境变量的三种方法】](http://yijiebuyi.com/blog/41ee3bab0c5bf1d43c7a8ccc7f0fe44e.html)


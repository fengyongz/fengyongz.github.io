---
layout: blog
istop: true
title: "利用github、jekyll打造个人github pages博客"
keywords: "github|jekyll|github pages|个人博客"
background-image: /style/images/BlankPortrait_20180426.png
date:  2018-04-26 14:30
category: jekyll博客
tags:
- github
- github pages
- jekyll
---
 
# 目的
 
记录和讲解如何利用github和jekyll来打造线上个人博客。

# 流程图

先看下流程图，了解具体步骤。
[![流程图]({{site.url}}/style/images/BlankPortrait_20180426.png)]({{site.url}}/style/images/BlankPortrait_20180426.png)

# 流程讲解

### github 注册账号和创建仓库

如果要使用github提供的二级域名，则必须与github的账户名相同，如果账户名为：xxxx,
那么个人博客的域名为xxxx.github.io，创建的仓库也名为：xxxx.github.io。

如果想使用自己的域名，则添加cname文件，修改github的setting等，可自行查找相关方法。

### 本地电脑与仓库文件

本地电脑先把仓库文件git clone下来。
为了方便git push不需要验证用户名和密码，这里需要用到ssh。

ssh-keygen生成key后，复制到github的用户设置setting-ssh里。
这样就可以直接git push。

### 本地电脑安装jekyll和下载主题

安装jekyll，可参考：
- [【jekyll中文官网】](http://jekyllcn.com/)
- [【教你使用Jekyll快速搭建个人博客】](https://www.jianshu.com/p/b27b98a2a774)

把jekyll文件添加到本地仓库文件夹。


### jekyll本地查看和添加到github仓库

这里需要配置nginx，添加本地https域名解析：https://xxxx.github.io。
因为线上github为https，所以本地也做https的处理。
- [【在本地开发环境(Mac)中安装自签名证书，启用https】](https://segmentfault.com/a/1190000012394467)


然后bundle exec jekyll build，生成本地文件，更改host查看博客效果。

然后git push到线上github仓库，进行查看。

如果github仓库查看页面报依赖包的错误，则按照推荐进行依赖包的调用修改。

### github自动生成jekyll的静态页面

jekyll文件里包含配置文件和依赖说明文件，github自身会根据这些文件自动生成静态页面在.site里面。
线上就部署完毕，以后在本地编写博客，本地查看，然后什么时候想推送上去就git push即可。


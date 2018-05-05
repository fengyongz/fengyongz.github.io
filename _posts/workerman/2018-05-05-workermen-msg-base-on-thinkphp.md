---
layout: blog
istop: true
title: "web站内实时消息-thinkphp结合workerman的web-msg-sender项目"
keywords: "thinkphp|workermen|web-msg-sender|实时消息"
background-image: /style/images/BlankPortrait_201805051.png
date:  2018-05-05 14:35
category: workermen 
tags:
- workermen
- thinkphp
- web-msg-sender
- 实时消息
---
 
# 目的
 
workerman是一个支持长连接的socket框架，基于php开发。web-msg-sender则是基于workerman二次开发的实时消息推送项目，不依赖nginx的单独运行程序。

由此，可用于网站的会员实时消息推送等功能，这里做一个原理的了解和记录。

# 说明图

先看下说明图，了解大概tp和workerman之间的相互联系。
[![说明图]({{site.url}}/style/images/BlankPortrait_201805051.png)]({{site.url}}/style/images/BlankPortrait_201805051.png)



### 配置workerman项目：web-msg-sender

- 检查wokerman所需的php扩展包：libevent、pcntl
- 下载web-msg-sender文件，composer install安装依赖包
- `php start.php start` 启动，socket监听2120+worker监听2123

    /usr/local/opt/php@5.6/bin/php start.php start

    Workerman[start.php] start in DEBUG mode
    ----------------------- WORKERMAN -----------------------------
    Workerman version:3.5.6          PHP version:5.6.35
    ------------------------ WORKERS -------------------------------
    user          worker        listen                   processes status
    von           PHPSocketIO   socketIO://0.0.0.0:2120   1         [OK]
    von           WebServer     http://0.0.0.0:2123       1         [OK]
    ----------------------------------------------------------------

监听2123的webserver，其实不需要，只是start.php里默认启动了,它还默认启动了监听接受推送信息的worker，监听2121，可通过查看端口进行检查。

    lsof -i:2121

    COMMAND   PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
    php     73170  von    6u  IPv4 0xf9ab00642fee1da7      0t0  TCP *:scientia-ssdb (LISTEN)

### 请求处理过程

- 客户端请求首页，nginx转发请求到tp项目里，解析后并返回响应。
- 首页含有ws请求，socket 2120接收请求并响应。
- tp项目触发推送消息动作，push msg给 worker 2121。
- worker 2121接收数据，推送消息到socket，socket传输数据实时在首页显示。

### 代码示例

访问：http://www.test.php 解析index.html，包含ws请求

/www.test.com/Application/Home/View/Index/index.html

    <strong id="count"></strong><h1 id="target"></h1>
    <script src="http://cdn.bootcss.com/jquery/3.1.0/jquery.min.js"></script>
    <script src='http://cdn.bootcss.com/socket.io/1.3.7/socket.io.js'></script>
    <script>
        jQuery(function ($) {
            // 连接服务端
            var socket = io('http://127.0.0.1:2120'); //这里当然填写真实的地址了
            // uid可以是自己网站的用户id，以便针对uid推送以及统计在线人数
            uid = 123;
            // socket连接后以uid登录
            socket.on('connect', function () {
                socket.emit('login', uid);
            });
            // 后端推送来消息时
            socket.on('new_msg', function (msg) {
                console.log("收到消息：" + msg);
                $('#target').append(msg).append('<br>');
            });
            // 后端推送来在线数据时
            socket.on('update_online_count', function (online_stat) {
                console.log(online_stat);
                $('#count').html(online_stat);
            });
        })
    </script>

访问：http://www.test.php/home/index/pushmsg?uid=123

触发推送消息动作，正常是业务逻辑触发，此处调试为手动触发

/www.test.com/Application/Home/Controller/IndexController.class.php

    <?php
    namespace Home\Controller;
    use Think\Controller;
    use Lib\Event\PushEvent;
    class IndexController extends Controller {
        public function index(){
            $this->display();
        }
        public function pushMsg(){
            $string = 'Man Always Remember Love Because Of Romance Only';
            $uid = isset($_GET['uid'])?$_GET['uid']:'';
            $push = new PushEvent();
            $push->setUser($uid)->setContent($string)->push();
        }
    }

为了PushEvent扩展类，新增一个扩展库，配置文件添加新的命名空间

/www.test.com/Application/Common/Conf/config.php

    <?php
        return array(
            //自定义扩展
            'Lib'     => APP_PATH.'Lib',
        );

/www.test.com/Application/Lib/Event/PushEvent.class.php
    
    <?php
        namespace Lib\Event;
        class PushEvent
        {
            protected $to_user = '';
            protected $push_api_url = 'http://127.0.0.1:2121/';
            protected $content = '';
            public function setUser($user = '')
            {
                $this->to_user = $user ? : '';
                return $this;
            }
            public function setContent($content = '')
            {
                $this->content = $content;
                return $this;
            }
            public function push()
            {
                $data = [
                    'type' => 'publish',
                    'content' => $this->content,
                    'to' => $this->to_user,
                ];
                $ch = curl_init ();
                curl_setopt($ch, CURLOPT_URL, $this->push_api_url);
                curl_setopt($ch, CURLOPT_POST, 1);
                curl_setopt($ch, CURLOPT_HEADER, 0);
                curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
                curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
                curl_setopt($ch, CURLOPT_HTTPHEADER, array('Expect:'));
                $res = curl_exec($ch);
                curl_close($ch);
                dump($res);
            }
        }

### 参考资料

- [【thinkphp5结合workerman的消息推送实例_基于web-msg-sender进行消息推送】](https://blog.csdn.net/h330531987/article/details/78081392)
- [【使用 web-msg-sender 进行实时消息通知推送】](http://www.ptbird.cn/web-msg-sender-send-content.html)
- [【基于workerman的集群推送例子】](https://blog.csdn.net/qq_28666081/article/details/51052100)










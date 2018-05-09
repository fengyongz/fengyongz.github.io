---
layout: blog
istop: true
title: "workerman深度接入thinkphp框架"
keywords: "thinkphp|workermen"
background-image: /style/images/BlankPortrait_20180509.png
date:  2018-05-08 11:35
category: workermen 
tags:
- workermen
- thinkphp
---
 
# 目的
 
把workerman深度接入thinkphp框架里，方便worker和redis、mysql之间的相互调用。

# 说明图

先看下说明图。
[![说明图]({{site.url}}/style/images/BlankPortrait_20180509.png)]({{site.url}}/style/images/BlankPortrait_20180509.png)

### 下载workerman

两种方法：

- 下载workerman源码包，thinkphp根目录新建vendor目录
- composer安装tp官方的workerman扩展包

先看下目录结构：

    |-Application
    |-vendor
        |-workerman
        |-autoload.php
    |-Thinkphp
    |-index.php
    ....

### 新建workerman的启动文件

新建一个`/Application/Home/Controller/WorkerController.class.php`,加载workerman，调用Worker类。

    <?php
        require_once APP_PATH.'../vendor/autoload.php';
        use Workerman\Worker;
        class WorkerController extends Controller {
            public function index(){
                if(!IS_CLI){
                    die("无法直接访问，请通过命令行启动");
                }
                \Workerman\Worker::$daemonize = true;
                \Workerman\Worker::$stdoutFile = '/tmp/stdout.log';
                $worker = new \Workerman\Worker('http://0.0.0.0:2121');
                $worker->count = 4;
                $worker->onWorkerStart = function($worker){
                    $worker->listen();
                };
                $worker->onMessage = function($connection, $data){
                    $data['post'] = json_encode($data['post']);
                    S('data', $data['post'], array('type'=>'Redis', 'port'=>6379));
                    return $connection->send('msg recieved');
                };
                \Workerman\Worker::runAll();
            }

这里直接演示了如何在workerman的闭包函数里直接调用tp的缓存redis方法。

### cli模式启动worker监听

thinkphp下cli模式启动：

    /usr/local/opt/php@5.6/bin/php index.php home/worker/index start

但是会发现启动不成功，其实是workerman的参数问题。独立的workerman启动命令是：
    php xxx.php start

第一个参数是启动文件，第二个参数是命令。但是在thinkphp下，多了home/worker/index的路径，所以需要更改workerman的源码，使它获取正确参数。

/vendor/workerman/workerman/Worker.php

    protected static function parseCommand()
    {
        ...
        /*if (!isset($argv[1]) || !in_array($argv[1], $available_commands)) {
            exit($usage);
        }
        $command  = trim($argv[1]);
        $command2 = isset($argv[2]) ? $argv[2] : '';*/
        if (!isset($argv[2]) || !in_array($argv[2], $available_commands)) {
            exit($usage);
        }
        $command  = trim($argv[2]);
        $command2 = isset($argv[3]) ? $argv[3] : '';
        ...

把命令参数start正确传入worker，这时候再cli命令启动，就可以看到：


    ----------------------- WORKERMAN -----------------------------
    Workerman version:3.5.6          PHP version:5.6.35
    ------------------------ WORKERS -------------------------------
    user          worker        listen               processes status
    von           none          http://0.0.0.0:2121   4         [OK]
    ----------------------------------------------------------------
    Input "php index.php stop" to stop. Start success.

### tp启动多个worker监听

如果想在tp里启动多个worker监听，例如在WorkerController.class.php增加一个index2方法，监听新的端口2122。

    <?php
        ...
            public function index2(){
                ...
                $worker = new \Workerman\Worker('http://0.0.0.0:2122');
                ...
                \Workerman\Worker::runAll();
            }

然后cli启动：`/usr/local/opt/php@5.6/bin/php index.php home/worker/index2 start`

发现启动不成功，这是因为workerman是依赖于启动文件的，worker的pid文件被设定为启动文件的路径转化。

/vendor/workerman/workerman/Worker.php

    ...
    protected static function init()
    {
        //static::$_startFile = $backtrace[count($backtrace) - 1]['file'];
        //$unique_prefix = str_replace('/', '_', static::$_startFile);

        static::$_startFile = $backtrace[2]['class'].'\\'.$backtrace[2]['function'];
        $unique_prefix = str_replace('\\', '_', static::$_startFile);


而放在tp里启动文件就变成了index.php，这样就导致index和index2的启动文件相同，因此pid文件也相同，就会相互覆盖。所以找到workerman的启动方init，通过dump调试把pid启动文件名改为`Home_Controller_WorkerController_index`：

    /vendor/workerman/Home_Controller_WorkerController_index.pid

这样每个方法都可以正常启动worker，各自也拥有自己的pid文件。

### 参考资料

- [【workerman源码分析之启动过程】](http://www.cnblogs.com/CpNice/p/4714182.html)
- [【workerman和thinkphp完美结合使用源码】](http://www.thinkphp.cn/code/2026.html)
- [【Laravel --- Laravel5.3 和 Workerman结合使用（异步）】](https://www.cnblogs.com/taotaoxixihaha/p/6689929.html)
- [【PHP SOCKET框架 - Workerman】](http://itopic.org/workerman.html)
- [【事件循环：JavaScript、Node.js、PHP-Swoole、PHP-workerman】](http://blog.ifeeline.com/2648.html)










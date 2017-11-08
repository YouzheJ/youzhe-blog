---
title: 深入浅出nodejs笔记(8-11)
date: 2017-11-08 16:33:45
tags:
- node
categories:
- 笔记
- node
---

### 第八章 构建web应用

<!--more-->

- 基础功能
    
    - 请求方法：req.method
    
    > PUT 新建资源，POST更新资源，GET 查看资源，DELETE删除资源

    - 路径解析：req.url （hash不会存在于报文的任何地方）
        
    - 查询字符串：?xxx=xxx&xxx=xxx
    
    ```js
    req.query = url.parse(req.url, true).query;
    
    /* 解析为json对象，如果键出现多次，值将会是一个数组
    {
        foo: 'bar',
        baz: ['val1', 'val2'] 
    }
    /*
    ```
    - Cookie: 浏览器和服务器可以实现状态的记录
    
        > cookie处理步骤：
        > - 服务器向客户端发送Cookie
        > - 浏览器将Cookie保存
        > - 之后每次浏览器都会将Cookie发向服务器端
        
        格式 Set-Cookie：name=value; Path=/; Expires=Sun, 23-Apr-23 09:01:35 GMT; Domain=.domain.com;
        
        > name=value必须外其他都是可选的。
        > Expires: 客户端、服务器时间可能不匹配，存在偏差
        > Max-Age: 过期时长
        > HttpOnly: 不允许通过document.cookie修改cookie
        > secure: http中无效，https中才有效，表示创建的cookie只能在https连接中被浏览器传递到服务器，http不传递
        
        > 问题：
        > - 体积过大
        > - 前后端都可以修改， 对敏感信息的保护是无效的
    
    - Session： 
    
        - 数据只保留在服务器端，客户端无法修改
        
        - 常见的两种方法：
        
            - 基于Cookie实现用户与数据的映射
            - 通过查询字符串实现浏览器端和服务器端数据的对应
    
        - session直接存在内存中可能会引起性能问题，可用第三方缓存如redis
        
        - Session与安全：让口令更加安全
            1. 通过私钥加密进行签名，如果签名非法，将服务器端的数据立即过期
            2. 另一种方法：将客户端的某些独有信息与口令作为原值进行签名，这样攻击者一旦不在原始的客户端上进行访问，就会签名失败。独有信息包括用户IP和用户代理。
            
            > XSS攻击获取cookie
            
        - 缓存
        
            > 缓存HTML、css等
        
            - 缓存规则:
                - 添加Expires或Cache-Control到报文头中：节省发起条件请求
                - 配置ETags: 会发起ETag条件请求（条件请求：If-Modified-Since/Last-Modified 或 If-None-Match/ETag）
                - 让Ajax可缓存
                
            > **大多数的缓存只应用在GET请求中**
            
            - 清除缓存：通过设定版本号更新

- 数据上传

    > 通过Transfer-Encoding或Content-Length即可判断请求中是否带有内容，在data事件中通过流接收
    
    ```js
    var hasBody = function (req) {
        return 'transfer-encoding' in req.headers || 'content-length' in req.headers;
    }
    ```
    - 表单数据
    
        - 默认的表单提交
        > Content-Type: application/x-www-form-urlencoded
            
        - 报文体内容格式与查询字符串相同
            
    - 其他格式
        
        - JSON
            > Content-Type: application/json
        
        - XML
            > Content-Type: application/xml
            
        > 注意：Content-Type中可能包含其他内容，判断时需要区分 Content-Type：application/json; charset=utf-8
        
    - 附件上传
        > 含有file类型控件的特殊表单，以及需要指定表单属性enctype为multipart/form-data
        
        报头：
            Content-Type: multipart/form-data; boundary=AaBo3x
            Content-Length: 18231
    
    - 数据上传与安全
    
        - 内存限制（两个方案）：
            - 限制上传内容大小，一旦超过限制，停止接收数据，并相应413状态码
            - 通过流式解析，将数据流导向到磁盘中，node只保留文件路径等小数据
            
            ```js
            // 限制大小, 有content-type通过其判断，没有则在data事件中判断
            var btyes = 1024;
            
            function (req, res) {
                var received = 0;
                var len = req.headers['content-length'] ? parseInt(req.headers['content-length'], 10): null;
                
                // 如果内容超过长度限制，返回请求实体过长的状态码
                if (len && len > bytes) {
                   res.writeHead(413);
                   res.end();
                   return;
                }
                
                // limit
                req.on('data', function (chunk) {
                    received += chunk.length;
                    if (received > bytes) {
                        // 停止接收数据，触发end()
                        req.destroy();
                    }
                });
                
                handle(req, res);
            }
            ```
        - CSRF
        > 通过添加随机值的方式：就是在session中赋予一个随机值，并将随机值发送给前端，在接收端做一次校验就能识别出伪造请求

- 路由解析

    - 文件路径型
        
        - 静态文件
        - 动态文件（如jsp等）
    
    - MVC
    - RESTful

- 中间件
- 页面渲染

##### （page 216）

---
### 第九章 玩转进程

- 服务器模型的变迁
    - 同步
    - 复制
    - 多线程
    - 事件驱动

- 多进程架构
    > child_process模块
    
    - Master-Worker模式（主从模式）
    
        ```js
        // worker.js
        var http = require('http');
        http.createServer(function (req, res){
            res.writeHead(200, {'Content-Type': 'text/plain'});
            res.end('Hello world\n');
        }).listen(Math.round((1 + Math.random()) * 1000, '127.0.0.1'));
        
        // master.js
        var fork = require('child_process').fork;
        var cpus = require('os').cpus();
        for (let i = 0;i < cpus.length; i++) {
            fork('./worker.js');
        }
        ```

        - 主进程不负责具体业务，负责调度和管理工作，较稳定
        - 工作进程负责具体业务
        
        通过fork()复制的进程是一个独立的进程，有独立而全新的V8实例，需要至少30s的启动时间和至少10M的内存，切记fork()进程的代价的昂贵的
        
    - 创建子进程
        1. spawn(): 启动一个子进程来执行命令
        1. exec(): 与spawn()类似，启动一个子进程来执行命令，有一个回调函数获知子进程的状况，可设置timeout
        1. fork(): 与spawn()类似，不同在于创建的node子进程只需指定执行的JavaScript文件模块即可，可设置timeout
        1. execFile(): 启动一个子进程来执行可执行文件
        
        > 设置timeout后，超时进程会被杀死
        > 可执行文件如果是JavaScript文件需要在行首添加：
        
        ```bash
        #!/usr/bin/env node
        ```
    - 进程间通信
    
        ```js
        // parent.js
        var cp = require('child_process');
        var n = cp.fork(__dirname + '/sub.js');
        
        n.on('message', function (m) { // 监听子进程传来的消息
            console.log('PARENT GOT MESSAGE: ', m)
        })
        
        n.send({hello: 'world'}); // 向子进程发送消息
        
        // sub.js
        process.on('message', function (m) {
            console.log('CHILD GOT MESSAGE: ', m);
        });
        process.send({foo: 'bar'});
        ```
        > 父子进程其实是通过IPC通道实现message和send传递消息
        
        - 进程间通信原理
            > IPC: 进程间通信，node中实现IPC通道的是管道（pipe）技术。
        
    - 句柄传递
        > 句柄是一种可以用来标识资源的引用
        
        在send方法的第二个可选参数就是句柄
        
        ```js
        child.send(message, [sendHandle])
        ```
        - 示例：直接将tcp服务发送给子进程，这里父子进程都可能出来请求
        
        ```js
        // 主进程
        var child = require('child_process').fork('child.js');
        
        var server - require('net').createServer();
        server.on('message', function (socket) {// 可以去掉，只由子进程处理
            socket.end('handled by parent\n');
        });
        server.listen(1337, function () {
            child.send('server', server); // 相当于父进程代理了子进程
            server.close(); // 关闭的话，可以实现所有子进程监听同一端口
        })
        
        // 子进程
        process.on('message', function (m, server) {
            if(m === 'server') {
                server.on('message', function (socket) {
                    socket.end('handle by child\n');
                })
            }
        })
        ```
- 集群稳定之路

    - 进程事件
        - message：
        - error: 子进程无法被复制创建、无法被杀死、无法发送消息会触发事件
        - exit: 子进程退出时触发，正常退出，第一个参数为退出码，否则为null，如果是kill()方法杀死，第二个参数表示杀死时的信号
        - close: 在子进程的标准输入输出流中止时触发，参数同exit
        - disconnect: 父进程或子进程调用disconnect()方法触发，调用时将关闭监听IPC通道
        
        > kill() 方法也能给子进程发送消息，kill()不能真正的将子进程杀死，只是给子进程一个信号
        
    - 自动重启： 监听子进程的exit事件获知退出信息，然后重新启动一个工作进程来继续服务
    
        ```js
        // master.js
        var fork = require('child_process').fork;
        var cpus = require('os').cpus();
        
        var server = require('net').createServer();
        server.listen(13370);
        
        // 限量重启，出现频繁重启，说明问题较严重，需要注意
        var limit = 10;
        var during = 60000;
        var restart = [];
        var isTooFrequently = function () {
            // 记录重启时间
            var time = Date.now();
            var length = restart.push(time); // push返回数组长度
            if(length > limit) {
                // 取出最后10个记录
                restart = restart.slice(limit * -1);
            }
            // 最后一次重启到前10次重启之间的时间间隔
            return restart.length >= limit && restart[restart.length - 1] - restart[0] < during;
        }
        
        var workers = {};
        var createWorker = function () {
            // 检查是否太过频繁
            if(isTooFrequently()) {
                // 触发giveup事件后，不再重启
                process.emit('giveup', length, during);
                return;
            }
            var worker = fork(__dirname + '/worker.js');
            // 启动新的进程
            worker.on('message', function (message) {
                if(message.act === 'suicide') {
                    createWorker();
                }
            })
            worder.on('exit', function () {
                console.log('Worker ' + worker.pid + ' exited.');
                delete worders[worder.pid];
            });
            // 句柄转发
            worder.send('server', server);
            worders[worder.pid] = worker;
            console.log('Create worker. pid: ' + worker.pid);
        }
        
        for(var i = 0; i < cpus.length; i++) {
            createWorker();
        }
        
        // 进程自己退出时，让所有工作进程退出
        process.on('exit', function () {
            for(var pid in workers) {
                worders[pid].kill();
            }
        })
        ```
        
        ```js
        // worker.js
        var http = require('http');
        var server = http.createServer(function (req, res) {
            res.writeHead(200, {'Content-Type': 'text/plain'});
            res.end('handle by child, pid is ' + process.pid + '\n');
        });
        
        var worder;
        process.on('message', function (m, tcp) {
            if(m === 'server') {
                worder = tcp;
                worder.on('connection', function (socket) {
                    server.emit('connection', socket);
                });
            }
        });
        
        process.on('uncaughtException', function (err) {
            // 未捕获的异常需要记录日志
            logger.error(err);
            // 向主进程发送自杀信号, 在异常进程退出前创建新的进程，防止所有进程同时异常，导致没有进程在工作
            process.send({act: 'suicide'});
            // 停止接收新的连接
            worder.close(function () {
                // 所有已有连接断开后，退出进程
                process.exit(1);
            });
            // 5秒后退出进程,防止长连接
            setTimeout(function () {
                process.exit(1);
            }, 5000);
        });
        ```
    - 负载均衡
    - 状态共享
    
- Cluster模块: 用以解决多核cpu的利用率问题，同时有较完善的API用以处理的健壮性问题

    > 一个cluster模块应用中，一个主进程只能管理一组工作进程

    ```js
    var cluster = require('cluster');
    var http = require('http');
    var numCPUs = require('os').cpus().length;
    
    if(cluster.isMaster) {
        // fork workers
        for(var i = 0; i < numCPUs; i++) {
            cluster.fork();
        }
        
        cluster.on('exit', function (worker, code, signal) {
            console.log('worker ' + worker.process.pid + ' died');
        });
    }else {
        // workers can share any TCP connection
        http.createServer(function (req, res) {
            res.writeHead(200);
            res.end('hello world');
        }).listen(8000);
    }
    ```
    > 上述代码建议使用
    
    ```js
    cluster.setupMaster({
        exec: 'worker.js'
    });
    ```
    > 代替 cluster.isMaster 判断
        
---
### 第十章 测试

- 单元测试

    - ...
    - 私有方法的测试: 使用rewire模块
    
        ```js
        var lib = rewire('../lib/index.js');
        var fn = lib.__get__('fn');
        ```
    - 工程化与自动化
        
        - *nix系统下可以使用Makfile工具来构建工程
        - 持续集成：使用travis-ci实现持续集成

- 性能测试

    - 基准测试： 统计多少时间执行多少次某个方法（benchmark模块）
    - 压力测试
        
        - 网络接口的压力测试： 吞吐率、响应时间、并发数（工具：ab、siege、http_load...）

---
### 第十一章 产品化

- 项目工程化

    - 目录结构
    - 构建工具
        - Makefile： 不能再windows下使用
        - grunt
    - 编码规范
    - 代码审查

- 部署流程
- 性能

    - 动静分离：node处理静态文件的能力不算突出，可以用Nginx或专业的CND来处理
    - 启用缓存
    - 多进程架构：cluster模块，社区的pm2模块等
    - 读写分离
    
        > 对于数据库，读取的速度大高于写入的速度
    
    - 日志
    - 监控报警
        - 监控
        - 报警：
            
            - nodemailer模块实现邮件的发送
---
title: 深入浅出nodejs笔记(5-7)
date: 2017-11-08 16:32:27
tags:
- node
categories:
- 笔记
- node
---

### 第五章 内存控制

<!--more-->

- V8的垃圾回收机制与内存限制

    - V8的内存限制：一般的后端开发语言没有内存限制，但是node通过JavaScript使用内存时只能使用部分内存（64位操作系统约为1.4G，32位系统约为0.7G）。这样的限制下，将会导致node无法直接操作大内存对象，如处理一个2G的文件
    
        > 原因：
        > - 表层：由于V8最初是为浏览器设计的，这个限制绰绰有余
        > - 深层：V8的垃圾回收机制的限制，垃圾回收需要花费时间，1.5G的堆在某些情况下有可能会达到1s以上
    
    - V8的对象分配
    
        V8中所有的JavaScript对象都是通过堆来进行分配的
        
        ```js
        process.memoryUsage() // 查看V8内存信息
        
        {
            rss: 2333666, // 进程的常驻内存部分
            heapTotal: 7258112, // 已申请到的内存
            heapUsed: 4324128, // 当前使用量
            external: 17851
        }
        ```
        > 修改限制：
        
        ```js
        // 在node启动时, 一旦生效后不能动态修改
        node --max-old-space-size=1700 test.js // 老生代，单位MB
        node --max-new-space-size=1024 test.js // 新生代，单位KB
        ```
    - V8的垃圾回收机制
    
        1. V8的主要垃圾回收算法：主要基于分代式垃圾回收机制。
        
            - V8的内存分代
                - 新生代：存活时间较短
                - 老生代：存活时间较长或常驻内存
                
            - Scavenge算法（以空间换时间）：新生代
            
                > 将内存一分为二，其中一半闲置，垃圾回收时，将存活对象复制到闲置空间，然后将两空间互换
                
                在垃圾回收前会检查对象是否进行过Scavenge回收或者To空间的内存占用比超过限制，如果是则晋升到老生代
        
            - Mark-Sweep & Mark-Compact
            
                - Mark-Sweep: 标记活着的对象，清除没有标记的对象（会有清理后内存不连续的问题）
                - Mark-Compact: 标记活着的对象，并往一边移动，然后直接清理掉边界外的内存
                
            - Incremental Marking
            
                由于垃圾回收需要将应用暂停下来，为了降低全堆垃圾回收带来的停顿时间，V8将标记改为增量标记
                
                还有延迟清理和增量式整理，来改善停顿时间
                
    - 查看垃圾回收日志
    
        ```js
        node --trace_gc xxx // 查看垃圾回收日志
        node --prof xxx // 查看V8执行时的性能分析数据(但是基本不具备可读性，需要使用工具)
        ```

- 高效使用内存

    - 作用域
        
        1. 标识符查找（变量名）：优先在当前作用域中查找，然后到上级作用域中查找，直到查到为止
        2. 作用域链：即上述
        3. 变量的主动释放：通过delete删除对象（可能干扰V8的优化） 或 通过赋值解除引用（推荐使用）
    
    - 闭包
    
        > **实现外部作用域访问内部作用域中的变量的方法**
        
- 内存指标

- 内存泄露

    > 实质： 应当回收的对象出现意外没有被回收，变成了常驻在老生代中的对象

    - 通常原因：
        1. 缓存（慎将内存当缓存）
        2. 队列消费不及时
        3. 作用域未释放
        
- 内存泄露排查

- 大内存应用

    - 使用stream模块操作大文件
    
    ```js
    // 读取一个文件，然后写入到另一个文件中
    var reader = fs.createReadStream('in.txt');
    var writer = fs.createWriteStream('out.txt');

    reader.on('data', function (chunk) {
        wirter.write(chunk);
    });
    reader.on('end', function () {
        writer.end();
    })
    // 或
    reader.pipe(writer);
    ```
    
---
### 第六章 理解Buffer

- Buffer结构

    > 占用的内存不是通过V8分配的，属于对外内存

    - 模块结构：c++实现性能部分，JavaScript实现非性能部分
    
    - Buffer对象：类似于数组，元素为16进制的两位数，即0-255的数值。
    
    ```js
    var buf = new Buffer(100);
    console.log(buf.length); // 100
    console.log(buf[10]); // 0-255的随机数（初始化的时候就已经生成）
    
    // 赋值，但不为0-255
    buf[20] = -100;
    buf[21] = 300;
    buf[22] = 3.1415;
    console.log(buf[20]); // 156
    console.log(buf[21]); // 44
    console.log(buf[22]); // 3  舍弃小数部分
    ```
    - Buffer内存分配
    
    > 在C++层面申请内存，在JavaScript中分配内存
    
- Buffer的结构

    - 字符串转Buffer
    ```js
    new Buffer(str, [encoding]); // 不传encoding时默认utf-8
    
    // 一个Buffer对象可以存储不同编码类型的字符串转码的值
    buf.write(string, [offset], [length], [encoding]); // 可以多次写入，注意编码的字节长度
    ```
    - Buffer转字符串
    
    ```js
    buf.toString([encoding], [start], [end]);
    ```

- Buffer的拼接

    - 乱码的产生: 读取文件时，是一段一段的方式传输，直接拼接会出现乱码
    - 通过setEncoding()解决，但是只能处理utf-8、base64和ucs-2/utf-16LE 3中编码
    - 正确的Buffer拼接
    
    ```js
    var chunks = [];
    var size = 0;
    res.on('data', function (chunk) {
        chunks.push(chunk);
        size += chunk.length;
    });
    res.on('end', function () {
        var buf = Buffer.concat(chunks, size);
        var str = iconv.decode(buf, 'utf8');
        console.log(str);
    })
    ```
    - Buffer与性能

    > highWaterMark的大小决定回触发系统调用和data事件的次数

---
### 第七章 网络编程

> 大多数编程语言需要专门的web服务器作为容器，ASP、ASP.NET需要IIS作为服务器，PHP需要搭建Apache或Nginx环境等，JSP需要Tomcat服务器等，但是node不需要额外的容器。

- 构建TCP服务

    - TCP：传输控制协议（传输层）
    
        OSI模型（七层协议）|（层）
        ---|---
        HTTP、SMTP、IMAP等 | 应用层
        加密/解密等 | 表示层
        通信连接/维持会话 | 会话层
        TCP/UDP | 传输层
        IP | 网络层
        网络特有的链路接口 | 链路层
        网络物理硬件 | 物理层
        
        TCP是面向连接的协议，显著特征是：在传输前需要3次握手形成会话
        
        > 三次握手：客户端 --> 请求连接 --> 服务器端 --> 响应 --> 客户端 --> 开始传输
    

- 构建UDP服务
    
    - UDP：用户数据包协议，与TCP的最大区别就是UDP不是面向连接的。TCP中连接一旦建立，所有的会话都基于连接完成，客户端要与另一个TCP服务通信，需要另创建一个套接字（socket）来完成连接。但在UDP中，一个套接字可以与多个UDP服务通信，不过在网络差的时候存在丢包严重的问题，常用于音频、视频等，DNS服务即是基于它实现的。

- 构建HTTP服务

    - HTTP：超文本传输协议
    
    - HTTP报文：报文头和报文体
    
- 构建webSocket服务

    - 与node完美配合：
        - WebSocket客户端基于事件的编程模型与node中的自定义事件相差无几
        - WebSocket实现了客户端与服务器端之间的长连接，而node事件驱动的方式十分擅长与大量客户端保持高并发连接
        
    - WebSocket协议：握手和数据传输
    
        - 握手：客户端请求服务器更换协议，服务器切换协议，建立连接
        - 数据传输
    
- 网络服务与安全
    - SSL：安全套接层，一种安全协议
    - TLS：安全传输层协议，IETF将SSL标准化的产物
    - node提供了3个安全模块：crypto、tls、https
        - crypto主要用于加密解密（SHA1，MD5等）
        - tls建立在TLS/SSL加密的TCP连接上

    - TLS/SSL
        - 密钥：TLS/SSL是一个公钥/私钥的结构，公钥加密传输的数据，私钥解密接收的数据，所以在建立安全传输前，客户端和服务器需要互换公钥。缺点：可能受到中间人攻击
            > node采用openssl实现TLS/SSL
            
        - 数字证书：通过CA（数字证书认证中心）为站点颁发证书，服务器端需要通过自己的私钥生成CSR（证书签名请求）文件，CA机构通过这个文件颁发属于该服务器端的签名证书
        
        > 自签名证书：自己扮演CA机构，给自己的服务器颁发签名证书
        
        - CA
        
        ```bash
        openssl genrsa -out ca.key 1024
        openssl req -new -key ca.key -out ca.csr
        openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
        ```

        - 服务器
            - 生成csr文件
        
            ```bash
            openssl req -new -key server.key -out server.csr
            ```
            - 申请签名
            
            ```bash
            openssl x509 -req -CA ca.crt -CAkey ca.key -CAcreateserial -in server.csr -out server.crt
            ```
            - 客户端：获取服务器端的证书，通过CA验证证书，验证服务器名称、ip地址等
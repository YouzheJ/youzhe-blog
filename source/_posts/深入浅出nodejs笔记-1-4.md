---
title: 深入浅出nodejs笔记(1-4)
date: 2017-11-08 16:30:31
tags:
- node
categories:
- 笔记
- node
---

### 第一章 简介

- 高性能web服务器的特点： 事件驱动、非阻塞I/O

<!--more-->

- 浏览器中： v8作为JavaScript引擎， WebKit作为布局引擎

- node特点：

1. 异步I/O（node中绝大多数的操作是已异步的方式执行的）
2. 事件与回调函数
3. 单线程
    - 优点： 不用想多线程那样在意同步问题，没有死锁存在，没有线程上下文交换所带来的性能上的开销
    - 缺点： 无法利用多核cpu，错误会导致整个应用退出，大量计算占用cpu导致无法继续调用异步cpu（子进程解决）

- node的应用场景：

1. I/O密集型： node利用事件循环的处理能力，而不是启动每一个线程为每一个请求服务，资源占用极少
2. cpu密集型

---
### 第二章 模块机制

- CommonJS规范：
1. 模块引用 require
2. 模块定义 exports
在模块中module对象代表模块自身，exports是module的属性
3. 模块标识

- node的模块实现

    - 步骤：
        1. 路径分析
        2. 文件定位
        3. 编译执行
    
    - 模块：
        - 核心模块：node提供
        - 文件模块：自己编写
    
    - 优先从缓存加载：node缓存的是编译和执行之后的对象，require对相同模块的二次加载都是采用缓存优先的方式（第一优先级）
    
    - 路径分析和文件定位
    
        1. 模块标识符分析
        
            - 核心模块： 优先级仅次于缓存加载
            - 路径形式的文件模块（以. .. / 开始的标识符）： require方法会将路径转为真实路径， 并以真实路径为索引， 然后将编译的结果存放到缓存中， 加载速度慢于核心模块
            - 自定义模块（非核心模块，也不是路径形式的标识符）： 在路径各级的node——modules目录中查找文件，直到根目录下的node_modules（这个查找策略就是**模块路径**），所以速度最慢
            
        2. 文件定位
        
            - 文件扩展名分析： 在不包含文件扩展名时， node会依次尝试扩展名（.js/.node/.json）， 这个过程需要用fs模块同步阻塞式地判断文件是否存在。
            - 目录分析和包： require时没有找到文件而是找到目录， node会将目录当做一个包处理，然后查找package.json文件， 没有的话会查找index文件， 仍然找不到就会按模块路径查找， 还是没有则抛出异常
            
        3. 模块编译
        
            > require.extensions 可以得到已有的扩展加载方式
            
            > 对于不支持的扩展，建议先编译成js文件后在加载，这样就不会将繁琐的编译加载等过程引入node的执行过程中
            
            - javascript模块的编译： 
            
            在编译过程中node对获得的js文件内容进行头尾包装， 在头部添加了:
            
            ```js
            (function (exports, require, module, __filename, __dirname) {
                var math = require('math');
                export.area = function (radius) {
                    return Math.PI * radius * radius;
                }
            });
            ```
            这样每个模块之间的作用域都进行了隔离
            
            - C/C++模块的编译：
            
            实际上 .node 的模块文件不需要编译
            
            - JSON文件的编译:
            
            如果用到json的配置文件， 可以直接使用require加载， 而不需要自己解析， 并且二次引入有缓存优势
            
- 核心模块
    
    - 编译过程： 
        
        1. 将所有js代码转换为C/C++的数组，js代码以字符串的形式存储在node的命名空间中，是不可执行的，在启动node进程时，js代码直接加载进内存中，核心模块直接定位到内存中，比普通的文件模块从磁盘中一处处查找要快得多
        2. 进行头尾包装，执行和导出exports对象（编译成功的模块缓存到ativeModule.cache对象上）
    
    - C/C++核心模块的编译过程

- C/C++扩展模块: (不同平台下编译成的文件不同， 虽然引用加载时都是 .node文件，windows平台：Visual C++ --> .dll, *nix平台: g++/gcc --> .so)

- 模块调用栈
- 包与NPM

    - 包： 由包结构和包描述文件组成
    
        - 包结构：包实际是一个存档文件，即一个目录直接打包为.zip或.tar.gz格式， 安装后解压还原为目录
        
        符合CommonJS规范的包目录应该包含如下文件：
        
        > - package.json: 包描述文件
        > - bin: 可执行二进制文件的目录
        > - lib: 存放JavaScript代码的目录
        > - doc: 文档的目录
        > - test: 单元测试用例

        - 包描述文件： package.json
        
        > - name: 可以包含 . _ - 但是不允许有空格，包名必须唯一
        > - description：描述
        > - version：版本号
        > - author: 包作者
        > - main: require时，会优先检查这个字段，将其作为包中其余模块的入口，如果不存在，会查找包目录下的index
        > - dependencies: 当前包需要的依赖
        > - devDependencies: 只需在开发时依赖
        > - script: 脚本说明对象
        > ...
        
    - NPM常用功能
    
        - 安装：
            - 全局安装：并不是将一个包安装为一个全局包，实际上， -g 是将一个包安装为全局可用的可执行命令
            - 从本地安装： npm install xxx --> xxx 可以是存档文件、url、目录
            
- 前后端公用模块
    - AMD 规范是commonJS模块规范的一个延伸
    
    ```js
    define(['dep1', 'dep2'], function(dep1, dep2) {
        return function () { }
    });
    ```
    > 与commonJS区别:
    > 1. 使用define定义模块， node是隐式包装， 都是为了隔离作用域
    > 2. 导出需要return
    
    - CMD 规范（更接近CommonJS规范）
    
    ```js
    define(function(require, exports, module) {
        var a = require('./a');
        ...
    });
    ```
    > 与AMD区别: 模块定义和依赖引入
    > 1. amd需要在声明模块的时候指定所有的依赖，通过形参传递依赖到模块中（依赖前置）
    > 2. cmd则在需要时才通过require引入（依赖就近）
        
    - 兼容多种规范
    
    ```js
    ;(function (name, definition) {
        // 检测上下文环境是否为AMD或CMD
        var hasDefine = typeof define === 'function',
            // 检测上下文环境是否为node
            hasExports = typeof module !== 'undefined' && module.exports;
            
        if (hasDefine) {
            // AMD或CMD环境
            define(definition);
        } else if (hasExports) {
            // 定义为普通Node模块
            module.exports = definition();
        } else {
          // 将模块的执行结果挂在window变量中， 在浏览器中this指向window对象
          this[name] = definition();
        }
    })('hello', function () {
        var hello = function () {};
        return hello;
    });
    ```
        
---
### 第三章 异步I/O
        
> 将异步作为主要编程方式和设计理念的， node是首个。

- 异步I/O的原因
    - 用户体验
    
    > 如果脚本的执行时间超过100毫秒，用户就会感到页面卡顿，以为网页停止响应。
    > -- 高性能JavaScript

    异步不会阻塞js的执行，而且具有并发的优势

    - 资源分配

    node利用单线程，避免多线程死锁、状态同步等问题，利用异步I/O让单线程远离阻塞，以更好地使用CPU

- 异步I/O实现现状

    - 阻塞I/O与非阻塞I/O
    
    > - 阻塞I/O 调用之后一定要等到系统内核层面完成所有操作后，调用才结束
    > - 非阻塞I/O 调用之后立即返回，但是没有需要的数据，需要重复调用I/O操作来确认是否完成（轮询），再获取数据
    
    - 理想的异步I/O
    
    在异步期间不需要进行轮询，直接处理下一个任务，只需在I/O完成后通过信号或回调将数据传递给应用程序
    
    - 现实的异步I/O
    
    增加一个线程执行轮询
    
- Node的异步I/O

    - 事件循环：判断是否有事件，有的话判断事件中是否有回调，循环判断直到没有事件（一次循环称为Tick）
    - 观察者：每个事件循环中有多个观察者，判断是否有事件的过程就是向这些观察者询问是否有要处理的事件，node中事件主要来源于网络请求、文件I/O等
    - 请求对象：
    - 执行回调（I/O线程池）：组装好请求对象，送入I/O线程池等待执行，这个异步I/O的第一部分，回调通知是第二部分
    
    > 完整流程：（win中的IOCP为例）
    > - **异步调用**: 开始 - 发起异步调用 - 封装请求对象 - 设置参数和回调函数 - 将请求对象放入线程池等待执行（到**线程池**） - 结束
    > - **线程池**：开始 - 线程可用 - 执行请求对象中的I/O操作 - 将执行完成的结果放在请求对象中 - 通知IOCP调用完成（到**事件循环**） - 归还线程 - 结束
    > - **事件循环**：开始 - 创建主循环 - 从I/O观察者取到可用的请求对象（**3**） - 取出回调函数和结果调用执行 - 获取完成的I/O交给I/O观察者（到**3**）
    
- 非I/O的异步API
    - 定时器： setTimeout、setInterval，与异步I/O类似，只是不需要I/O线程池的参与
    
    > 定时器的问题在于它并非精确的，如果cpu被占用可能就会出现超时的情况

    - process.nextTick 
    
    > 立即异步执行下一个操作，而setTimeout(fn, 0)较为浪费性能
    
    - setImmediate 与process.nextTick类似，但优先级setImmediate较低
    
- 事件驱动与高性能服务器
    
    > 几种服务器模型：
    > - 同步式：一次只能处理一个请求
    > - 每进程/每请求：为每个进程启动一个进程
    > - 每线程/每请求：为每个进程启动一个线程，相比进程要轻量，但是大并发请求时仍然不行
    
    node通过事件驱动的方式处理请求，无需为每个请求创建额外的线程，因此线程上下文切换导致的开销较少

---
### 第四章 异步编程

- 函数式编程

    - 高阶函数
    - 偏函数用法：通过指定部分参数来产生一个新的的定制函数
    
- 异步编程的优势与难点

    - 优势：
    - 难点：
        1. 异常处理，try...catch... 不能捕获异步的异常，所以node会将异常作为回调函数的第一个实参传回，如果为空值，则表明异步没有异常抛出
        2. 函数嵌套过深
        3. 阻塞代码
        4. 多线程编程
        5. 异步转同步

- 异步编程的解决方案

    1. 事件发布/订阅模式
        
        events模块，具有方法：addListener/on()、once()、removeListener()、removeAllListeners()、emit()等
    
        - 继承events模块
        
        > util模块
        
        ```js
        var events = require('events');
        
        function Stream() {
            events.EventEmitter.call(this);
        }
        util.inherits(Stream, events.EventEmitter);
        ```

        - 利用事件队列解决雪崩问题
        > 例如用once解决高并发访问SQL数据库的问题
        
        ```js
        var proxy = new events.EventEmitter();
        var status = "ready";
        var select = function (callback) {
            proxy.once("selected", callback);
            if (status === "ready") {
                status = "pedding";
                db.select("SQL", function (results) {
                    proxy.emit("selected", results);
                    status = "ready";
                });
            }
        }
        ```
        - 多异步之间的协作方案
        > 偏函数
    
    2. Promise/Deferred模式
    
    3. 流程控制库

- 异步并发控制

    > 由于并发容易实现，但是下层服务器的性能可能会吃不消，因此需要过载保护

    1. bagpipe的解决方案
    
    2. async的解决方案
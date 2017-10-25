---
title: nodejs 使用笔记
date: 2017-10-25 14:29:40
tags:
- node
- pm2
categories:
- 笔记
- node
---

> [从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)

<!--more-->

### 入门

- Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎
- Node.js使用事件驱动，非阻塞I/O模型，轻量、高效，可以完美地处理时时数据，运行在不同的设备上

#### nodejs开发环境centos
> 使用nvm安装

1. 下载nvm wget https://github.com/cnpm/nvm/archive/v0.23.0.tar.gz
2. 解压 tar -xf v0.23.0.tar.gz
3. 安装nvm 

```bash
./install.sh
// 设置自启动
vi ~/.bash_profile

# nvm
source ~/nvm-0.23.0/nvm.sh
```

4. nvm --version 查看是否安装成功
5. nvm list-remote 查看可安装版本
6. nvm install v7.0.0 安装相应版本的nodejs
7. nvm ls 查看已安装的nodejs
8. nvm use vxxx 切换版本
9. nvm alias default v0.12.4 设置默认版本

#### nodejs开发环境Ubuntu
> 从github下载源码安装

0. 如果已安装失败，卸载已装的node和npm

```
sudo apt-get autoremove npm
sudo apt-get autoremove nodejs
```

1. 安装git：sudo apt-get install git
2. 从github下载源码
```
git clone git://github.com/joyent/node.git
```
3. 进入node
```
cd node
pwd
/home/conan/workspace/nodejs/node
```
4. 切换最新的release的版本v0.11.2-release
```
~ git checkout v0.11.2-release
Branch v0.11.2-release set up to track remote branch v0.11.2-release from origin.
Switched to a new branch 'v0.11.2-release'
```
5. 进行安装
```
./configure
make
sudo make install
```
6. 安装完成，查看node版本
```
node -v
-bash: /usr/bin/node: No such file or directory
```
提示错误，没有找到node，查一下node安装位置
```
~ whereis node
node: /usr/local/bin/node
```
7. 增加软链接：node和npm到/usr/bin
```
~ sudo ln -s /usr/local/bin/node /usr/bin/node
~ sudo ln -s /usr/local/bin/npm /usr/bin/npm
```
8. 我们再查看node和npm版本
```
~ node -v
v0.11.2
~ npm -v
1.2.21
```
9. 安装express
```
sudo npm install express -g
```

> 建立express工程，启动第一个项目

```
express -e nodejs-demo
```
- 安装依赖包
```
cd nodejs-demo
sudo npm install
```
- 启动程序
```
node app.js // express 改为 npm start
Express server listening on port 3000
```
- 测试是否启动成功curl
```
~ sudo apt-get install curl
~ curl localhost:3000

<!DOCTYPE html>
<html>
<head>
<title>Express</title>
<link rel='stylesheet' href='/stylesheets/style.css' />
</head>
<body>
<h1>Express</h1>
<p>Welcome to Express</p>
</body>
</html>
```

- nodejs的服务器日志：
```
GET / 200 6ms - 206b
```


#### Express4.x
- 全局安装express-generator
```
npm install -g express-generator@4
```

#### 使用hexo在github上构建博客
- Hexo是一个简单的、轻量的、基于node的静态博客框架。
- 在win7中安装
```
npm install -g hexo
```
- 创建项目

```
hexo init nodejs-hexo // 创建项目

cd nodejs-hexo // 进入目录

hexo server // 启动项目
```
- 发布

```
// 在_config.yml
deploy:
    type: git
    repo: git@.....
```

```bash
npm install hexo-deployer-git --save

hexo deploy
```

- 指定二级域名(blog.youzhej.cn)
    - 在source目录下创建CNAME文件，放在这不用每次创建
    ```
    // 写下需要指定的域名
    blog.youzhej.cn
    ```
    - 到域名的控制台
        - 设置主机记录github，类型A，到IP 199.27.76.133
        - 设置主机记录youzhej.github.io，类型CNAME，到github.youzhej.cn.
        - 设置主机记录blog，类型CNAME，到 youzhej.github.io

- 使用主题(jacman)
    - 安装
    ```
    git clone https://github.com/wuchong/jacman.git themes/jacman
    ```
    - 启用
    ```
    // _config.yml
    theme: jacman
    stylus:
        compress: true
    ```
    - 更新
    
    ```
    cd themes/jacman
    git pull origin master
    ```


#### bug解决参考
[http://www.jianshu.com/p/465830080ea9](http://www.jianshu.com/p/465830080ea9)

#### 用pm2守护node进程

[http://www.cnblogs.com/chyingp/p/pm2-documentation.html](http://www.cnblogs.com/chyingp/p/pm2-documentation.html)

- 安装

```
npm install -g pm2
```

- 用法

```
$ npm install pm2 -g     # 命令行安装 pm2 
$ pm2 start app.js -i 4  # 后台运行pm2，启动4个app.js 
                         # 也可以把'max' 参数传递给 start
                         # 正确的进程数目依赖于Cpu的核心数目
$ pm2 start app.js --name my-api # 命名进程
$ pm2 list               # 显示所有进程状态
$ pm2 monit              # 监视所有进程
$ pm2 logs               # 显示所有进程日志
$ pm2 stop all           # 停止所有进程
$ pm2 restart all        # 重启所有进程
$ pm2 reload all         # 0 秒停机重载进程 (用于 NETWORKED 进程)
$ pm2 stop 0             # 停止指定的进程
$ pm2 restart 0          # 重启指定的进程
$ pm2 startup            # 产生 init 脚本 保持进程活着
$ pm2 web                # 运行健壮的 computer API endpoint (http://localhost:9615)
$ pm2 delete 0           # 杀死指定的进程
$ pm2 delete all         # 杀死全部进程
```

- 运行进程的不同方式

```
$ pm2 start app.js -i max    # 根据有效CPU数目启动最大进程数目
$ pm2 start app.js -i 3      # 启动3个进程
$ pm2 start app.js -x        #用fork模式启动 app.js 而不是使用 cluster
$ pm2 start app.js -x -- -a 23   # 用fork模式启动 app.js 并且传递参数 (-a 23)
$ pm2 start app.js --name serverone  # 启动一个进程并把它命名为 serverone
$ pm2 stop serverone       # 停止 serverone 进程
$ pm2 start app.json        # 启动进程, 在 app.json里设置选项
$ pm2 start app.js -i max -- -a 23                   #在--之后给 app.js 传递参数
$ pm2 start app.js -i max -e err.log -o out.log  # 启动 并 生成一个配置文件
你也可以执行用其他语言编写的app  ( fork 模式):
$ pm2 start my-bash-script.sh    -x --interpreter bash
$ pm2 start my-python-script.py -x --interpreter python
```
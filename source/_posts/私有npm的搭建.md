---
title: 私有npm的搭建
date: 2017-10-23 17:59:03
tags:
- node
- npm
categories:
- 笔记
- node
- npm
---

#### sinopia

> #### 现在作者已经停止维护, 可以改为使用 Verdaccio

[https://segmentfault.com/a/1190000005790827#articleHeader0](https://segmentfault.com/a/1190000005790827#articleHeader0)

<!--more-->

- 安装

```
npm install -g sinopia
```

- 启动

```
sinopia
```
如果能访问: http://localhost:4873/, 即成功

- 或pm2启动

```
npm install -g pm2
pm2 start `which sinopia`
```

- 服务端配置
> Sinopia的特点是，你在哪个目录运行，它的就会在对应的目录下创建自己的文件

> #### 如果要通过外网访问需要在配置文件最后添加

```
listen: 0.0.0.0:4873
```

config.yaml配置文件
```
# path to a directory with all packages
storage: ./storage  //npm包存放的路径

auth:
  htpasswd:
    file: ./htpasswd   //保存用户的账号密码等信息
    # Maximum amount of users allowed to register, defaults to "+inf".
    # You can set this to -1 to disable registration.
    max_users: -1  //默认为1000，改为-1，禁止注册

# a list of other known repositories we can talk to
uplinks:
  npmjs:
    url: http://registry.npm.taobao.org/  //默认为npm的官网，由于国情，修改 url 让sinopia使用 淘宝的npm镜像地址
    
packages:  //配置权限管理
  '@*/*':
    # scoped packages
    access: $all
    publish: $authenticated

  '*':
    # allow all users (including non-authenticated users) to read and
    # publish all packages
    #
    # you can specify usernames/groupnames (depending on your auth plugin)
    # and three keywords: "$all", "$anonymous", "$authenticated"
    access: $all

    # allow all known users to publish packages
    # (anyone can register by default, remember?)
    publish: $authenticated

    # if package is not available locally, proxy requests to 'npmjs' registry
    proxy: npmjs

# log settings
logs:
  - {type: stdout, format: pretty, level: http}
  #- {type: file, path: sinopia.log, level: info}

# you can specify listen address (or simply a port) 
listen: 0.0.0.0:4873  ////默认没有，只能在本机访问，添加后可以通过外网访问。
```

- 添加用户

```
npm adduser --registry http://localhost:4873
```

- 错误: 413

> 修改配置文件
```
max_body_size: xxmb
# 默认10mb
```

---
#### cnpm
[https://segmentfault.com/a/1190000000368906](https://segmentfault.com/a/1190000000368906)
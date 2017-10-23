---
title: nginx 笔记
date: 2017-10-23 11:41:34
tags:
- nginx
categories:
- 笔记
- nginx
---

#### 安装

<!--more-->

[http://www.linuxidc.com/Linux/2016-09/134907.htm](http://www.linuxidc.com/Linux/2016-09/134907.htm)

- gcc 安装

```
yum install gcc-c++
```
- PCRE pcre-devel 安装

```
yum install -y pcre pcre-devel
```

- zlib 安装

```
yum install -y zlib zlib-devel
```

- openssl 安装

```
yum install -y openssl openssl-devel
```

- 下载nginx

```
wget -c https://nginx.org/download/nginx-1.10.1.tar.gz
```

- 解压

```
tar -zxvf nginx-1.10.1.tar.gz
cd nginx-1.10.1
```

- 配置

```
1.使用默认配置

./configure

2.自定义配置（不推荐）

./configure \
--prefix=/usr/local/nginx \
--conf-path=/usr/local/nginx/conf/nginx.conf \
--pid-path=/usr/local/nginx/conf/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi
注：将临时文件目录指定为/var/temp/nginx，需要在/var下创建temp及nginx目录
```

- 编译安装

```
make
make install

查找安装路径：

whereis nginx
```

- 启动、停止nginx

```
cd /usr/local/nginx/sbin/
./nginx 
./nginx -s stop
./nginx -s quit
./nginx -s reload
./nginx -s quit:此方式停止步骤是待nginx进程处理任务完毕进行停止。
./nginx -s stop:此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程。

查询nginx进程：

ps aux|grep nginx
```

- 重启 nginx

```
1.先停止再启动（推荐）：
对 nginx 进行重启相当于先停止再启动，即先执行停止命令再执行启动命令。如下：

./nginx -s quit
./nginx
2.重新加载配置文件：
当 ngin x的配置文件 nginx.conf 修改后，要想让配置生效需要重启 nginx，使用-s reload不用先停止 ngin x再启动 nginx 即可将配置信息在 nginx 中生效，如下：
./nginx -s reload
```

- 开机自启动

```
即在rc.local增加启动代码就可以了。

vi /etc/rc.local
增加一行 /usr/local/nginx/sbin/nginx

设置执行权限：
chmod 755 rc.local
```

--- 
#### 使用教程
[http://www.cnblogs.com/jingmoxukong/p/5945200.html](http://www.cnblogs.com/jingmoxukong/p/5945200.html)

- 将二级域名绑定到同一ip的不同端口


1. 将二级域名映射到服务器的ip

2. 修改配置: (默认位置 /usr/local/nginx/conf/nginx.conf)

```
...
http {
    ...
    upstream test_server {
        server localhost:8080;
    }
    ...
    server {
        ...
    }
    server {
        listen          80;
        server_name     xxx.yuming.com www.xxx.yuming.com;
        location / {
            proxy_pass       http://test_server;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```

---
#### 错误处理

- ngnix.pid not found: 需要杀掉nginx

- 413

> 在配置文件中的 http | server | location 添加一下内容: 
```
client_max_body_size     8m; # 默认为1m
```
> 注意: reload 可能不起效, 需要杀掉重启

> 查看端口
```
netstat -nltp
```

> 杀掉进程

```
# 根据pid杀掉
kill -9 xxx

# 强制杀掉
pkill -9 nginx
```

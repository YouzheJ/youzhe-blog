---
title: 搬瓦工vps
date: 2017-10-23 11:46:45
tags:
- vps
categories:
- 笔记
- vps
---

搬瓦工KVM洛杉矶直连VPS购买及优化

[http://www.bawagon.com/kvmvps/](http://www.bawagon.com/kvmvps/)

### 搬瓦工优化

<!--more-->

优化流程

有人会问，BBR和锐速那个更好呢？个人认为由于本地网络的不同，我们可以分别安装BBR和锐速测试一段时间，得出适合自己网络的加速方式。

一 安装锐速

这里需要注意的是如果选择使用锐速，系统就需要重新安装，并且系统就不能选择后缀带-bbr的系统，如centos-6-x86_64-bbr（这是错误的选择）。 

重装步骤为登录KiwiVM先 stop 当前系统，再 Install new OS为不带bbr后缀的系统，比如debian-7-x86_64（这是正确的选择）。

目前搬瓦工更新了centos 6 7系统内核，不匹配锐速需要先更换内核再安装锐速。

 
1.1 更新系统(必做)
centos 系统运行 (目前仅不支持centos-6-x86 其他centos版本不带bbr都可以 )
```
yum install wget net-tools -y && wget --no-check-certificate http://soft.paozailushang.com/vps/huanneihe.sh && bash huanneihe.sh

# debian ubutun 系统运行
apt-get update -y
```

1.2  更新系统后安装锐速
```
wget --no-check-certificate -O appex.sh https://raw.githubusercontent.com/0oVicero0/serverSpeeder_Install/master/appex.sh && chmod +x appex.sh && bash appex.sh install
```
 
1.3  卸载锐速
```
wget --no-check-certificate -O appex.sh https://raw.githubusercontent.com/0oVicero0/serverSpeeder_Install/master/appex.sh && chmod +x appex.sh && bash appex.sh uninstall
```

然后我们按照图片的数据指示，一路回车就可以了。 安装完锐速记得要重启服务器一次即 reboot

现在打开你的浏览器试试速度吧
vultr
如果你安装没有效果，编辑以下命令，新手可以使用winscp编辑：putty及winscp初级使用方法
```
vi /appex/etc/config
```
然后rsc和maxmode设置参数修改为1，gso="0" （重要：经测试搬瓦工这个需要设置为0）然后在进行重启
```
/appex/bin/serverSpeeder.sh reload
/appex/bin/serverSpeeder.sh restart
```
常用命令
启动锐速：
```
/appex/bin/serverSpeeder.sh start
```
停止锐速：
```
/appex/bin/serverSpeeder.sh stop
```
查看锐速是否正常运行
```
/appex/bin/serverSpeeder.sh status
```
检查是否有appex0模块：lsmod
```
lsmod
```

二 安装谷歌BBR
2017.3.23 搬瓦工太贴心了，你会在系统重装发现搬瓦工KVM自带安装好BBR的centos系统。我们不需要自己重新安装配置了。


所以，下面的就作为保留纪念吧。
使用root用户登录，运行以下命令：
```
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
```
安装完成后，脚本会提示需要重启 VPS，输入 y 并回车后重启。

重启完成后，进入 VPS，验证一下是否成功安装最新内核并开启 TCP BBR，输入以下命令：
```
uname -r
```
查看内核版本，含有 4.10 就表示 OK 了
```
   sysctl net.ipv4.tcp_available_congestion_control
   ```
返回值一般为：
net.ipv4.tcp_available_congestion_control = bbr cubic reno
 ```
sysctl net.ipv4.tcp_congestion_control
```
返回值一般为：
net.ipv4.tcp_congestion_control = bbr
 ```
sysctl net.core.default_qdisc
```
返回值一般为：
net.core.default_qdisc = fq
 
 ```
lsmod | grep bbr
```
返回值有 tcp_bbr 模块即说明bbr已启动。
附个SSR一键脚本：
```
wget http://soft.paozailushang.com/vps/SSR.sh && chmod +x SSR.sh && ./SSR.sh 2>&1 | tee ssr.log
 ```
 
 - 安装wget
```
yum -y install wget
```
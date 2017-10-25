---
title: linux 使用笔记
date: 2017-10-25 14:36:52
tags:
- linux
categories:
- 笔记
- linux
---

### ubuntu & centos 使用笔记

<!--more-->

#### 终端的使用：
快捷键：ctrl+alt+T

#### 常用命令：
- **安装** 如安装git：sudo apt-get install git
- sudo: 以其他身份运行，默认root
- pwd：查看当前工作目录


### centos

- 安装wget
```
yum -y install wget
```

- 用户

```
# 新建
adduser yourname
passwd yourname

# 切换用户
su -  # 切换至root用户
su username

# 给用户配置sudo
# 切换至root用户
visudo
# 找到root ALL=(ALL) ALL
# 在下面增加xxx ALL=(ALL) ALL
# 保存即可
```

- 安装git

```bash
sudo yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker  # 安装依赖
yum -y install git # 安装git
```

- linux权限说明

查看权限：在linux终端输入

```bash
ll
# 或
ls -l xxx.xxx

# 会显示如下格式:
-rwx-r--r-- ...
```

"-rwx-r--r--"说明：

第一位属于管理员， 与权限无关
2-4位属于当前用户
5-7位属于同组用户
8-10位属于其他用户

然后：
r ==> 可读 r = 4
w ==> 可写 w = 2
x ==> 可执行 x = 1

-rwx-r--r-- => 744
-rwx-r-xr-x => 755
rwxr-rwx-rwx => 777

- 修改权限

```bash
chmod 777 xxx.xxx # 修改文件或目录的权限为777即 -wxr-rwx-rwx
chmod 777 -R xxx # 修改文件夹下的所有文件的权限为777即 -wxr-rwx-rwx
```
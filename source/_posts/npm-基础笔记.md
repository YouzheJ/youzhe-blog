---
title: npm 基础笔记
date: 2017-10-23 17:56:13
tags:
- npm
- node
categories:
- 笔记
- node
- npm
---

npm依赖关系图 [https://exploringdata.github.io/vis/npm-packages-dependencies/](https://exploringdata.github.io/vis/npm-packages-dependencies/)

#### npm 查看全局安装过的包
```
npm list -g --depth 0
```

<!--more-->

#### 查看是否安装或使用过某个插件
```
npm list [name] //亲测可用 

npm [name] -v //貌似没用，只是查看到npm的版本号
```

#### 查看包可用的版本
```
npm view [name] version  // 查看包可用版本
npm view [name] versions  // 查看包所有可用的版本
```

#### cnpm
由于墙的原因，连接npm有时会很慢，此时可以使用淘宝镜像npm--cnpm
1. 安装cnpm
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
2. 使用 cnpm 代替 npm 即可
    
    如：cnpm install --> npm install

#### 删除某个包
```
npm remove <name>
```

### 使用nrm快速切换源
```
npm install -g nrm
```
- 列出可选源
```
nrm ls
```
- 切换
```
nrm use taobao // 切换为淘宝源
```
- 增加源
```
nrm add <registry> <url> [home]
```
- 删除源
```
nrm del <registry>
```
- 测试速度
```
nrm test // 测试所有
nrm test npm // 测试npm
```

### 发布npm包
- 开发
```
$ npm init // 初始化package.json
/*
* 开发包的功能
*/
```
- 发布
```
// 先确保源切换为npmjs.org， 可使用nrm进行快速切换
npm adduser // 添加账号
npm whoami // 查看当前账号
npm publish // 发布， 可在后面加上版本号 --tag 0.0.1 ?? 不能使用
```
- 更新
```
// 例：在1.0.0 版本上修改
npm version patch // 1.0.1

// 继续修改，并发布一个prerelease版本
npm version prerelease 
npm publish --tag -beta//版本n-n-n-n@1.0.2-0
```
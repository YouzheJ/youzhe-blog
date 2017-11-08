---
title: 'nodejs中使用redis笔记'
date: 2017-11-08 16:00:07
tags:
- node
- redis
categories:
- 笔记
- node
- redis
---

### redis

[redis命令](https://redis.io/commands)

[http://www.yiibai.com/redis/redis_quick_guide.html](http://www.yiibai.com/redis/redis_quick_guide.html)

<!--more-->

> redis桌面工具: [https://redisdesktop.com/download](https://redisdesktop.com/download)
---

- Redis是一个开源，高级的键值存储和一个适用的解决方案，用于构建高性能，可扩展的Web应用程序
- 主要有三个特点，使它优于其他键值数据存储系统：
    - Redis将其数据库完全保存在内存中，仅使用磁盘进行持久化
    - 与其它键值数据存储相比，Redis有一组相对丰富的数据类型
    - Redis可以将数据复制到任意数量的从机中

> redis存储的是键值对

- Ubuntu中安装

```bash
sudo apt-get update
sudo apt-get install redis-server
```

- 启动

```bash
redis-server
```

-检查是否正在工作

```bash
redis-cli
// 会打开一个redis提示
redis 127.0.0.1:6397>

ping
# PONG # 启动成功
```
> redis中的命令是不区分大小写的

- #### Redis中的数据类型

- 字符串

```bash
set key 'string' # key是键 'string' 是对应的值
get key
```

- 散列/哈希: 键值对的集合，用于表示对象

```bash
HMSET ukey username "yiibai" password "passwd123" points 200
```

- 列表: 字符串列表, 按插入顺序排序

```bash
lpush listname value
lrange listname 0 10 # 显示listname中0-10的元素
```

- 集合: 字符串的无序集合，是不重复的字符串集合

```bash
sadd listname value
smembers listname # 显示listname中的元素
```

- 可排序的集合: 类似于集合，是不重复的字符串集合

```bash
zadd setname 0 value0
zadd setname 0 value1
zadd setname 1 value2
zadd setname 1 value2
ZRANGEBYSCORE setname 0 1000 # 显示0-1000中的元素

# value0
# value1
# value2
```

- 在远程服务器上运行命令

```bash
redis-cli -h host -p port -a password
```

- 删除键

```bash
DEL akey 
# (integer)1 删除成功
# (integer)0 删除失败
```

- 部分命令

```bash
EXISTS key # 检查键是否存在
EXPIPE key seconds # 设置过期时间，秒
EXPIPEAT key timetamp # 设置过期时间，时间戳
PEXPIPE key milliseconds # 设置过期时间，毫秒
KEYS pattern # 查找与指定模式相匹配的所有键
PERSIST key # 删除指定键的过期时间
RENAME key newkey # 更改键的名称
PTTL key # 获取键到期的剩余时间（毫秒为单位）
TYPE key # 返回存储在键中的值的数据类型
```

---
### 在node中使用
> 使用redis包（此包中的命令与redis一一对应）

```js
var redis = require('redis'),
    client = redis.createClient();

// 监听错误
client.on('error', function (err) {
    console.log('Error: ' + err);
});
```

- 结合bluebird使用Promise

```js
var redis = require('redis'),
    bluebird = require('bluebird');
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);
```

- get

```js
// 普通使用
client.get('foo', function (err, res) {
    console.log(res);
});

// 使用Promise
return client.getAsync('foo').then(function (res) {
    console.log(res);
});
```

- set

```js
client.set("some key", "some val", cb);
client.set(["some other key", "some val"], cb);
```

- 设置多个值

```js
client.hmset(key, {a:'a', b: 'b', c: 'd'}, callback);
```

- 获取键对应的多个值

```js
client.hgetall(key, function (err, res) {
    
});
```

- 设置过期时间

```js
client.set('key', 'value', 'EX', 10); // 10秒后过期
```

- 判读redis中是否存在

```js
let result = client.exists('key');
// 0 已存在，不操作  1 不存在，插入值
```
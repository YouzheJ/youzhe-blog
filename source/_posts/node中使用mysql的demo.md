---
title: node中使用mysql的demo
date: 2017-11-08 16:07:17
tags:
- node
- mysql
categories:
- 笔记
- node
- mysql
---

- 一个简单的例子

<!--more-->

```js
const originMysql = require('mysql');
const Promise = require('bluebird');

class mysql {
  constructor() {
    this.database = 'spider';
    this.table = 'product_list';
    this.connection = originMysql.createConnection({
      host: 'localhost',
      user: 'root',
      password: '',
      database: this.database,
    });
  }
  connect () {
    return new Promise((resolve, reject) => {
      this.connection.connect((err) => {
        if(err) {
          reject(err);
          console.log('链接失败');
        }else {
          resolve(this.connection);
          console.log('链接成功');
        }
      });
    });
  }
  deleteTable (table) {
    return new Promise((resolve, reject) => {
      this.connection.query(`DROP TABLE ${table}`, (err, res) => {
        if(err) {
          reject(err);
          return console.log('删除表失败');
        }else {
          console.log('删除表成功');
          resolve(res);
        }
      });
    });
  }
  createTable (table) {
    return new Promise((resolve, reject) => {
      // 先删除旧表
        this.connection.query(
          `CREATE TABLE ${table}(id int PRIMARY KEY AUTO_INCREMENT, shop_host varchar(255), data varchar(2048))`,
          function (err, result) {
            if(err) {
              reject(err);
              console.log('建表失败');
            }else {
              resolve(result);
              console.log('建表成功');
            }
          }
        );
    }).catch(async err => {
      // 失败的话则删除表重新建
      await this.deleteTable('product_list');
      await this.createTable('product_list');
    });
  }
  insert (hostname, data) {
    return new Promise((resolve, reject) => {
      this.connection.query(`INSERT INTO ${this.table} VALUES(null, '${hostname}', '${data}')`, function (err, result) {
        if(err) {
          reject(err);
          console.log('插入失败')
        }else {
          resolve(result);
          console.log('插入成功')
        }
      });
    });
  }

  select () {
    return new Promise((resolve, reject) => {
      this.connection.query(`select * from ${this.table}`, function (err, result) {
        if(err) {
          reject(err)
          console.log('查询失败');
        }else {
          resolve(result)
          console.log(result)
        }
      });
    });
  }
}

// 测试
// (async () => {
//   const mysql1 = new mysql();
//   await mysql1.connect();
//   // await mysql1.deleteTable('product_list');
//   await mysql1.createTable('product_list'); // 建表
//   await mysql1.insert('hhaa','111');
//   await mysql1.insert('hhss','222');
//   await mysql1.insert('hhdd','333');
//   await mysql1.select();
// })().catch(err => console.log(err));

module.exports = mysql;
```

- 一个使用连接池的例子

```js
const originMysql = require('mysql');
const Promise = require('bluebird');

// 连接池
class mysqlPool {
  constructor (opt = {}) {
    this.connectionLimit = opt.connectionLimit || 30;
    this.host = opt.host || 'localhost';
    this.user = opt.user || 'root';
    this.password = opt.password || '';
    this.database = opt.database || 'spider';
    this.tablename = opt.tablename || 'default';

    // 初始化mysql pool
    this.pool = originMysql.createPool({
      connectionLimit: this.connectionLimit,
      host: this.host,
      user: this.user,
      password: this.password,
      database: this.database,
    });

    this.pool.on('release', function (connection) {
      console.log('释放连接');
    });
  }
  // 获取连接
  getConnection () {
    return new Promise((resolve, reject) => {
      this.pool.getConnection((err, connection) => {
        if(err) {
          console.log('获取连接失败');
          reject(err);
          return false;
        }
        console.log('获取连接成功');
        resolve(connection);
      });
    });
  }
  // 释放连接
  release (connection) {
    return new Promise((resolve, reject) => {
      connection.release();
    });
  }
  deleteTable (table, connection) {
    return new Promise((resolve, reject) => {
      connection.query(`DROP TABLE ${table}`, (err, res) => {
        if(err) {
          reject(err);
          return console.log('删除表失败');
        }else {
          console.log('删除表成功');
          resolve(res);
        }
      });
    });
  }
  createTable (table, connection) {
    return new Promise((resolve, reject) => {
      // 先删除旧表
        connection.query(
          `CREATE TABLE ${table}(id int PRIMARY KEY AUTO_INCREMENT, shop_host varchar(255), data varchar(2048))`,
          function (err, result) {
            if(err) {
              reject(err);
              console.log('建表失败');
            }else {
              resolve(result);
              console.log('建表成功');
            }
          }
        );
    }).catch(async err => {
      // 失败的话则删除表重新建
      await this.deleteTable('product_list');
      await this.createTable('product_list');
    });
  }
  // 插入行
  insert (hostname, data, connection) {
    return new Promise((resolve, reject) => {
      connection.query(`INSERT INTO ${this.tablename} VALUES(null, '${hostname}', '${data}')`, (err, res) => {
        if(err) {
          reject(err);
          return console.log('插入失败');
        }
        console.log('插入成功');
        resolve(res);
      });
    });
  }
  // 显示表中的所有内容
  select (connection) {
    return new Promise((resolve, reject) => {
      connection.query(`select * from ${this.tablename}`, function (err, result) {
        if(err) {
          reject(err);
          console.log('查询失败');
        }else {
          console.log(result);
          resolve(result);
        }
      });
    });
  }
}

// 测试
// (async () => {
//   const mysqlPool1 = new mysqlPool({tablename: 'product_list'});
//   const connection = await mysqlPool1.getConnection();
//   // mysqlPool1.deleteTable('product_list', connection); // 删除表
//   await mysqlPool1.insert('hhtt', '111111', connection)
//     .catch(async err => {
//       err.code === 'ER_NO_SUCH_TABLE' 
//         && await mysqlPool1.createTable('product_list', connection)
//       await mysqlPool1.insert('hhtt', '111111', connection);
//     });
//   await mysqlPool1.insert('hhff', '222222', connection);
//   await mysqlPool1.insert('hhcc', '333333', connection);
//   await mysqlPool1.select(connection);
//   await mysqlPool1.release(connection);
// })().catch(err => console.log(err));

module.exports = mysqlPool;
```
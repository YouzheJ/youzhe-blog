---
title: 'mongoDB与mongoose'
date: 2017-11-08 16:13:53
tags:
- node
- mongodb
- mongoose
categories:
- 笔记
- node
- mongodb
---

#### mongoDB & mongoose
windows 配置mongoDB
[http://www.jianshu.com/p/6b1b32f7fab7](http://www.jianshu.com/p/6b1b32f7fab7)

<!--more-->

- 启动mongoDB服务：
```
d:\mongodb\bin\mongod.exe --dbpath d:\mongodb\data\db
```
- 创建windows服务
    > 利用两个脚本文件运行”启动“和”停止“

    - 在mongodb下创建文件夹logs，在logs文件夹下创建mongodb.log文件
    - 在mongodb下创建文件夹data，在data文件夹下创建db文件夹
    - 按下”win“键，在搜索框中输入”cmd.exe“,按下”ctrl +shift+enter“键进入管理员模式下的cmd窗口。然后在>后输入"d:\mongodb\bin\mongod.exe --logpath d:\mongodb\logs\mongodb.log --logappend --dbpath d:\mongodb\data\db --serviceName MongoDB --install"按下”enter键“
    - 然后在>后输入”net start MongoDB“，会看到”MongoDB 服务正在启动  MongoDB服务启动成功“，后输入services.msc可查看服务启动情况
    - 执行mongo.exe进行操作查看
    - start | stop 批处理文件创建
        - 创建两个txt文档，分别命名为startMongoDB.bat 和stopMongoDB.bat 。文档内内容编辑为
        ```
        net start MongoDB
        
        net stop MongoDB
        ```
    
#### ubuntu

- 安装

```
sudo apt-get install mongodb
```
- 查看版本

```
mongo -version
```

- 启动/关闭

```
service mongodb start
service mongodb stop
```
- 查看是否启动

```
pgrep mongo -l
```

- 卸载

```
sudo apt-get purge remove mongodb-clients mongodb-server
```

---

#### mongodb shell 命令

- 输入 mongo 进入shell模式
- show dbs: 显示数据库列表
- show collections: 显示当前数据库中的集合
- show users: 显示所有用户
- use yourDB: 切换当前数据库至yourDB
- db.help(): 显示数据库操作命令
- db.yourCollection.help(): 显示集合操作命令， yourCollection是集合名
- mongodb中没有创建数据库的命令， use xxx， 再做一些操作， 即可创建

#### 一个例子

- 切换数据库

```
user School
```

- 创建集合

```
db.createCollection('teacher') # 可以不创建
```

- 插入数据

```
# 两种形式
db.student.insert({_id: 1, sname: 'zhangsan', sage: 20}) #_id 可选
db.student.save({_id: 1, sname: 'zhangsan', sage: 20}) #_id 可选
```
> 不同： 添加_id时，如果_id已存在，insert不做操作， save做更新操作

- 查找数据

```
db.yourCollection.find(criteria, filterDisplay)
#                    查询条件，可选     筛选， 可选

db.student.find()  #查询所有记录。相当于：select * from student
db.student.find({sname: 'lisi'})  #查询sname='lisi'的记录。相当于： select * from student where sname='lisi'
db.student.find({},{sname:1, sage:1}) #查询指定列sname、sage数据。
                                      #相当于：select sname,sage from student。
                                      #sname:1表示返回sname列，默认_id字段也是返回的，
                                      #可以添加_id:0（意为不返回_id）写成{sname: 1, sage: 1,_id:0}，就不会返回默认的_id字段了
db.student.find({sname: 'zhangsan', sage: 22}) #and 与条件查询。相当于：select * from student where sname = 'zhangsan' and sage = 22
db.student.find({$or: [{sage: 22}, {sage: 25}]}) #or 条件查询。相当于：select * from student where sage = 22 or sage = 25
```

- 修改数据

```
db.youCollection.update(criteria, objNew, upsert, multi ) 
# criteria: update的查询条件，类似sql update查询内where后面的 
# objNew : update的对象和一些更新的操作符（如$set）等，也可以理解为sql update查询内set后面的。 
# upsert : 如果不存在update的记录，是否插入objNew，true为插入，默认是false，不插入。 
# multi: mongodb默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。默认false，只修改匹配到的第一条数据。 
# 其中criteria和objNew是必选参数，upsert和multi可选参数 

db.student.update({sname: 'lisi'}, {$set: {sage: 30}}, false, true) #相当于：update student set sage =30 where sname = 'lisi';
```

- 删除数据

```
db.student.remove({sname: 'chenliu'}) #相当于：delete from student where sname='chenliu'
```

- 退出shell

```
exit
# or Ctrl + C
```

---

#### api
[https://cnodejs.org/topic/548e54d157fd3ae46b233502](https://cnodejs.org/topic/548e54d157fd3ae46b233502)

#### nodejs中使用mongoose (demo)
[http://blog.fens.me/nodejs-mongoose-json/](http://blog.fens.me/nodejs-mongoose-json/)

- 安装mongoose
```
npm install mongoose
```

- 增加models目录
    - 增加mongodb.js
    ```
    var mongoose = require('mongoose');
    mongoose.connect('mongodb://localhost/nodejs'); // 数据库名：nodejs
    exports.mongoose = mongoose;
    ```
    - 增加Movie.js
    ```
    var mongodb = require('./mongodb');
    var Schema = mongodb.mongoose.Schema;
    var MovieSchema = new Schema({
    name : String,
    alias : [String],
    publish : Date,
    create_date : { type: Date, default: Date.now},
    images :{
    coverSmall:String,
    coverBig:String,
    },
    source :[{
    source:String,
    link:String,
    swfLink:String,
    quality:String,
    version:String,
    lang:String,
    subtitle:String,
    create_date : { type: Date, default: Date.now }
    }]
    });
    var Movie = mongodb.mongoose.model("Movie", MovieSchema);
    var MovieDAO = function(){};
    module.exports = new MovieDAO();
    ```
    
    - 在app.js中增加访问路径
    ```
    app.get('/movie/add',movie.movieAdd);//增加
    app.post('/movie/add',movie.doMovieAdd);//提交
    app.get('/movie/:name',movie.movieAdd);//编辑查询
    ```
    
    - 在routes目录中，增加movie.js
    ```
    var Movie = require('./../models/Movie.js');
    exports.movieAdd = function(req, res) {
    if(req.params.name){//update
    return res.render('movie', {
    title:req.params.name+'|电影|管理|moive.me',
    label:'编辑电影:'+req.params.name,
    movie:req.params.name
    });
    } else {
    return res.render('movie',{
    title:'新增加|电影|管理|moive.me',
    label:'新增加电影',
    movie:false
    });
    }
    };
    exports.doMovieAdd = function(req, res) {
    res.send({'success':true});
    };
    ```
    
#### 模糊查询
- mongodb
    - 精确查询
    ```
    db.movies.find({'name':'xxx'})
    ```
    - 模糊查询(正则)
    ```
    db.movies.find({'name':/xxx/})
    ```
- mongoose
    - 查询所有
    ```
    MovieDAO.prototype.findByName = function(query, callback) {
      Movie.findOne(query, function(err, obj){
        callback(err, obj);
      });
    };
    ```
    - 模糊查询
    ```
    exports.movie = function(req, res) {
      var query={};
      if(req.query.m2) {
        query['name']=new RegExp(req.query.m2);//模糊查询参数
      }
    
      Movie.findByName (query,function(err, list){
        return res.render('admin/movie', {movieList:list});
      });
    }
    ```
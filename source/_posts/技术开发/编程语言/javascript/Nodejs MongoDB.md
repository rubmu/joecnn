---
title: Nodejs MongoDB
date: 2013/12/26
tags: [javascript]
---

### 1. MongoDB 是什么？
MongoDB 是一个基于分布式文件存储的中小型数据库，与 Oracle 完全不一样，采用的是文档存储形式(NoSQL). 

### 2. 搭建 MongoDB 环境
1. 将下载的 mongodb.rar 解压。 
2. 在环境变量中配置 $mongodb_home/bin 
3. 在同一盘符下创建 E: data/db 
4. Cmd ==> mongod  //启动 mongodb 服务 

```bash
# 进程启动成功提示信息
Tue Feb 11 23:30:11.512 [initandlisten] MongoDB starting : pid=2564 port=27017 dbpath=\data\db\ 64-bit host=PC 
```

### 3. 使用 MongoDB
1. 保持 mongod 服务，在另一 cmd 中使用 mongo 进入 
2. 创建数据库：use k9 
3. 创建文档 users：因为要使用 mongoose 组件包，所以文档后到要加一个 's' 

```bash
db.users.insert({uname:'admin',pwd:'asdf'});  //插入后即创建文档 
db.users.find();   //查询全部 
```

### 4. 使用 Mongoose 
Mongoose 是在 Nodejs 异步环境下对 mongodb 进行便捷操作的对象模型工具。

#### 4.1 安装 Mongoose 组件

```bash
npm install -g express-mongoose
npm install -g mongoose 
```
#### 4.2 定义 VO 类
```javascript
/*  MongoDB models */
var mongoose = require('mongoose');
var schema = mongoose.Schema;  //模式
var UserSchema = new schema({
    uname:String,
    pwd:String
});
exports.User = mongoose.model('User',UserSchema);   //与 Users 表关联
```

#### 4.3 在程序中使用
1. 引用组件：`var mongoose = require('mongoose');`
2. 加入VO组件：`var models = require('./models');`
3. 使用User模型：`var User = models.User;`
4. 连接数据库：`mongoose.connect('mongodb://localhost/k9');`
5. 修改登录验证： 
```javascript
exports.doLogin = function(req, res){
    var u_u = {uname:req.body.uname, pwd:req.body.pwd};
    User.count(u_u,function(err,doc){  
        //根据u_u查询结果
        if(doc==0){
            res.redirect('/login'); 
        }else{
            res.redirect('/welcome?uname='+u_u.uname); 
        }
    });
};
```

> 注意：提示 mongoose 未安装，则需要将 mongoose，express-mongoose 拷至当前项目下 
例子：E:\Nodejs\test\myproject 
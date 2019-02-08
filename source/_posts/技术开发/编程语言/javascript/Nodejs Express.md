---
title: Nodejs Express
date: 2013/12/26
tags: [javascript]
---

### 1. Express 是什么？
Express 是一个Node 最常用的开发包，用于快速构建 Web 项目。

### 2. 安装 Express
npm install -g express@3  //全局包 

> 注：无法使用 express 时，将 node_modules 目录加入环境变量 path 中 
设置永久环境变量即使重启机器也能够使用node命令了。  
>>进入/etc vi profile在最后面追加两行： 
>> ```bash
>> export NODE_HOME=/home/node-v0.10.0-linux-x64 
>> export PATH=$PATH:$NODE_HOME/bin 
>> export NODE_PATH=$NODE_HOME/lib/node_modules 
>> ```

> express4.x 以上版本，命令行工具已经分离出来 
> 1. npm install -g express-generator  //安装命令行工具 
> 2. express project1 && cd project1  //创建项目 
> 3. npm install   //安装express及依赖 
> 4. npm start  //启动 

### 3. 创建测试项目 

```bash
CD E:\Nodejs\test 
Express -e myproject  //创建项目 
```

目录结构： 
- Node_modules：存放所有项目依赖项 
- Package.json：项目依赖项配置及开发者信息 
- App.js：程序启动文件 
- Public：静态文件(css、js、img) 
- Routes：路由文件 
- Views：页面文件(ejs模板)

### 4. 启动 express test 
`Node app.js `
> 提示 express 未安装 
==> 将 $home\AppData\npm\node_modules\express  文件拷至项目目录下 
提示 ejs 未安装 
==>npm install ejs 

### 5. 修改ejs模板
Ejs view 模板默认以 .ejs 结尾，修改默认模板，修改为 html 模板。 
```javascript
App.js 
==>  var ejs = require('ejs'); 
==>  app.engine('.html',ejs__express); 
==>  app.set('view engine','html'); 
```

### 6. 安装 supervisor  debug环境 
Supervisor 组件包，可以动态加载修改后的开发程序，无需重新启动 Node。 
`Npm install -g supervisor`

### 7. 简单登录Demo
例==> E:\Nodejs\test\myproject 
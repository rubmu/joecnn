---
title: Nodejs quickstart
date: 2013/12/26
tags: [javascript]
---

### 1. Nodejs 是什么？
> Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine.

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 

### 2. 安装 Nodejs
1. Nodejs.org 上下载 source code 
2. 进入目录, 解压来 source  `Tar -zxvf  nodejs-v0.10.tar.gz `
3. 进入目录, 检查安装环境 `./configure `

> 可以使用 `./configrue --prefix /nodejs` 指定安装目录 , 提示 gcc 未安装, 使用 yum -y install gcc+ gcc-c++ 安装gcc

4. 编译安装 `make && make install`
5. 创建软连接 `sudo ln -s /nodejs/bin/node /usr/local/bin/node` 在任意目录使用

### 3. 卸载 Nodejs
1. 卸载npm  `Npm uninstall npm`
2. 移除文件 `rm -rf bin/node bin/node-waf include/node lib/node lib/pkgconfig/nodejs.pc share/man/man1/node.1`
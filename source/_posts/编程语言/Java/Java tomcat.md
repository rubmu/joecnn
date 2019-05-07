---
title: 在Idea中配置Tomcat
date: 2013/12/26
tags: [java, ide]
---

### 1. 安装 Tomcat

1. 进入官网 http://tomcat.apache.org 下载文件
2. 解压文件
3. 运行 `bin/startup` 即可启动，端口默认为 `8080`

### 2. 设置环境变量

```bash
CATALINA_BASE=D:\Java\apache-tomcat-9.0.2
CATALINA_HOME=D:\Java\apache-tomcat-9.0.2
ClassPath=;%CATALINA_HOME%\lib\servlet-api.jar;
Path=;%CATALINA_HOME%\bin;%CATALINA_HOME%\lib;
```

验证：在命令行中运行 `startup`

### 3. 在 Idea 中配置 Tomcat

1. 进入 `Run - Edit Configurations`, 添加一个 `Local Tomcat Server`

2. 在 `Configure` 中定位到本地的 Tomcat

3. 添加 `Deployment` 项目，定位到打包好的 war 包

4. 启动配置好的 `Tomcat Server`

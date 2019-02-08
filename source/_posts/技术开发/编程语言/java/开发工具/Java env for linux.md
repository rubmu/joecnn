---
title: Java env for linux
date: 2014/10/16
tags: [java, linux]
---

##### 1. 解压文件

```bash
$ mkdir /usr/local/java
$ tar -zxf /mnt/jre-8u151-linux-x64.tar.gz -C /usr/local/java/
```

##### 2. 设置环境变量

```bash
$ vi /etc/profile

>>>>>>
JAVA_HOME=/usr/local/java/jre1.8.0_151  
PATH=$PATH:$JAVA_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:$CLASSPATH  
export PATH
<<<<<<<

$ source /etc/profile
```

##### 3. 验证环境

```bash
$ java -version
```

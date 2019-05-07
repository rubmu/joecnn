---
title: JDK 多版本环境共存
date: 2019/2/16 22:00
tags: [java]
---

在 JDK8 之前的版本需要手动设置环境变量 `JAVA_HOME`. JDK8 多了自动配置环境变量, 所以想要多版本 JDK 共存并可以进行自由切换，需要先删掉默认环境。

### 1. 删除自动配置的环境

自动配置的环境变量指向的是一个隐藏目录 `C:\ProgramData\Oracle\Java\javapath`, 删除这个目录下的3个 `.exe` 文件，系统就无法匹配到了。

### 2. 安装多版本 JDK

进行多个版本的 JDK 安装。

### 3. 设置系统环境变量

```bash
# 指向 jdk6 目录
JAVA6_HOME = D:\Java\jdk_6

# 指向 jdk8 目录
JAVA8_HOME = D:\Java\jdk_8

# 设置默认的 jdk, 进行切换版本
JAVA_HOME = %JAVA8_HOME%

```
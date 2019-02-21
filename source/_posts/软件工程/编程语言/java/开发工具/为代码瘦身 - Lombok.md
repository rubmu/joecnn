---
title: 为代码瘦身 - Lombok
date: 2017/03/19
tags: [java, ide]
---

lombok 提供了简单的注解的形式来帮助我们简化消除一些必须有但显得很臃肿的 java 代码。

### 一、lombok 介绍
lombok 在编译生成的字节码文件中添加 getter 和 setter 方法，性能与手动编写相同！ 

### 二、lombok 安装
使用 lombok 是需要安装的，如果不安装，IDE 则无法解析 lombok 注解。
- 1.下载 [lombok.jar](https://projectlombok.org/download.html) 包
- 2.运行Lombok.jar:  java -jar D:\software\lombok.jar
> 数秒后将弹出一框，以确认eclipse的安装路径
- 3.确认完eclipse的安装路径后，点击install/update按钮，即可安装完成
- 4.安装完成之后，请确认eclipse安装路径下是否多了一个lombok.jar包，并且其配置文件eclipse.ini中是否 添加了如下内容:
```bash
-javaagent:lombok.jar 
-Xbootclasspath/a:lombok.jar
```
如果上面的答案均为true，那么恭喜你已经安装成功，否则将缺少的部分添加到相应的位置即可

- 5.重启eclipse或myeclipse

### 三、lombok 使用注解
maven 引入依赖
```xml
<dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok</artifactId>
   <scope>provided</scope>
</dependency>
```
- @Data   ：注解在类上；提供类所有属性的 getting 和 setting 方法，此外还提供了equals、canEqual、hashCode、toString 方法
- @Setter：注解在属性上；为属性提供 setting 方法
- @Getter：注解在属性上；为属性提供 getting 方法
- @Slf4j ：注解在类上；为类提供一个 属性名为log 的 logback 日志对象
- @NoArgsConstructor：注解在类上；为类提供一个无参的构造方法
- @AllArgsConstructor：注解在类上；为类提供一个全参的构造方法
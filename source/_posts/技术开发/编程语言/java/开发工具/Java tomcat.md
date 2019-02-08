---
title: Java tomcat
date: 2013/12/26
tags: [java, ide]
---

1. 安装jdk（不可中文路径）.
2. 配置环境变量 ：
 (1) JAVA_HOME ：D:\Program Files\Java\jdk1.6.0_10；
 (2) PATH: ;%JAVA_HOME%\bin;
 (3) CLASS_PATH :%JAVA_HOME%\lib\tools.jar;%JAVA_HOME%\lib\dt.jar;
3. 将 tomcat6.0 解压拷至  D:\Program Files\Java;
4. 配置 tomcat6.0
 (1) TOMCAT_HOME : D:\Program Files\Java\tomcat6.0;
 (2) CATALINA_HOME : D:\Program Files\Java\tomcat6.0;
 (3) CLASS_PATH : %CATALINA_HOME%\common\lib\servlet.jar;
 (4) PATH : ;%TOMCAT_HOME%\bin;
